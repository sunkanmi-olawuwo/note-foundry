# Note Foundry: implementation map from the existing templates

**Conclusion:** reuse the existing LangChain gateway, LangGraph checkpointer, database, storage, typed frontend/API connection, and test infrastructure. Build the knowledge-bank domain and its controlled generation/review workflow around them. There is no need to introduce another provider integration or agent framework.

Investigation date: 6 November 2026. Remote main refs were checked again and are unchanged from the previous assessment: base `7c056c804a568696f9498619417c9f22357c9ed3`, FastAPI `11398774187b2e54ae4a204624c1735b896e8e5f`, Next.js `4e9d5717ec4c8215a9f0beebb7ce4c1ef90aa42a`.

This is an implementation proposal, not implemented application behaviour. No template or application runtime code was changed, and no paid LLM calls were made. This investigation was subsequently incorporated into the repository as documentation. The earlier local results remain applicable to the unchanged snapshots: 28 backend-template tests, 95 frontend-template tests, combined rendering, and Compose configuration validation passed. The deeper investigation below read application code, tests, migrations, and integration points; it did not rerun the full generated application or live-provider evaluations.

## 1. Reuse and extension map

Paths in this table are generated-project paths, relative to the repository root.

| Existing code | Verified responsibility | What Note Foundry needs |
| --- | --- | --- |
| `backend/src/app/llm/gateway.py` | Builds the configured chat model; exposes `model()`, budgeted embeddings, `agent()`, and call metadata; provides `gateway.checkpointer`. | Use this gateway in graph nodes. Add task output schemas, composed project prompts, and application-run/call correlation. Preserve the existing budget guard. |
| `backend/src/app/llm/checkpoints.py` | Async PostgreSQL checkpoint saver, dedicated pool, checkpoint migrations integrated into Alembic. | Compile a Note Foundry graph with this checkpointer. Associate each graph thread with an authorised application run. Implement pause/resume and recovery tests. |
| `backend/src/app/llm/prompts.py` | Loads repository prompt files; records a content hash through call metadata. | Store versioned project instructions and composed-prompt snapshots alongside the base prompt version. A base-file hash alone does not identify the project instructions sent. |
| `backend/src/app/llm/budget.py` | Reserves and settles daily global/user spend; stores individual calls, feature, user and prompt fields. | Map application run/stage attempts to call IDs; add per-run limits if required. Do not describe existing global/user caps as project/run caps. |
| `backend/src/app/llm/embeddings.py` | Async embeddings API under the same budget; batches requests and returns vectors in input order. | Build chunk creation, embedding persistence, versioning, deduplication, scoped retrieval, and evaluation. The embeddings client itself does not index documents. |
| `backend/src/app/db.py` and `migrations/` | Async engines/sessions, named constraints, readiness, and separate template/application migration branches. | Add project, source, note, review, job, and search models using the application branch. Import product models through `app.models` so autogeneration sees them. |
| `backend/src/app/storage.py` | S3-compatible object access, signed upload/download helpers, size/type constraints. | Build actual authorised upload-initiation/completion endpoints, file metadata verification, source-version registration, and controlled preview/render delivery. The example upload route in its docstring is not a shipped endpoint. |
| `backend/src/app/api/__init__.py` | Versioned `/v1` router; currently includes the example identity route. | Register project, format, source, job, note, review, search, and artifact routes. Keep business rules in reusable services. |
| `backend/src/app/auth.py` and `frontend/src/lib/auth.ts` | Local development identity and production hooks that fail closed. | Integrate a real session/token provider and backend verification; enforce project access on every transport and resume/read/write operation. |
| `frontend/src/lib/api/` and `src/app/api/[...path]/route.ts` | Typed browser/server clients and a same-origin backend proxy. | Generate types from Note Foundry endpoints. Use asynchronous job endpoints and progress polling. Preserve the established frontend-session/backend-credential boundary. |
| `frontend/src/styles/tokens.css` and frontend tests | Reusable design tokens, accessibility checks, and screenshot fixtures for the shell. | Create product screens, source previews, note blocks, diagram display, editing and review interactions; extend accessibility and screenshot coverage. |
| `backend/tests/`, replay cassettes, and `tests/evals/` | Tests for model calls, spending, tool loops, checkpoints, storage, and provider behaviour. | Add Note Foundry datasets and criteria for explanation quality, citation support, completeness, diagram accuracy, document layout, and regression behaviour. |

## 2. Existing LangChain/LangGraph support is substantive

`Gateway.agent()` already uses LangChain `create_agent`, the configured budgeted model, the PostgreSQL checkpointer, and a step limit. The template's own documentation explicitly describes custom `StateGraph` workflows whose nodes call `gateway.model()` and compile with `gateway.checkpointer`.

The agent tests script a tool call, read saved graph state from PostgreSQL, assert budget settlement, and check the recursion limit. Live evaluation definitions cover structured output, streamed usage settlement, tool execution and persistence, and budget refusal. Those live evaluations were inspected, not executed for this assessment.

For Note Foundry's defined processing stages, implement a custom workflow. The autonomous `agent()` helper remains available for future tasks that actually need model-directed tool selection.

## 3. Important constraints discovered in code

### PDF and vision inputs

The budget guard accepts recognised image blocks, but its `_UNBOUNDED_BLOCKS` explicitly rejects document/file/audio/video content blocks. Therefore raw PDF upload-to-model is not a supported path through this gateway today.

Use the PDF handler to extract text, identify image-only pages, and render bounded page images where needed. Test the selected model's image inputs and the gateway's reservation accounting. Keep references back to original pages and regions. A future raw-file feature requires a deliberate accounting change.

### Output limits and task models

The configured default is 1,024 output tokens per call. This is appropriate to revisit for section drafting, not an allowance for a complete long study guide. Use small stages, explicit schemas, truncation detection, per-stage limits and run budgets. The gateway builds one configured chat model; multiple reviewer/drafter models are an optional extension, not an existing per-project model-routing feature.

### Requests are shorter than document-generation jobs

The Next.js proxy has a 60-second request timeout. Starting a generation job must return quickly with a job ID. The worker should execute the graph outside the HTTP request, and the interface should read progress and findings through ordinary endpoints. A review pause releases active work; resumption is a new authorised command.

### Storage transport and previews

The API's default request limit is 1 MiB; source binaries are intended to go directly to object storage through signed URLs. The default signed upload limit is 25 MiB, with a generic media-type allowlist. Add the exact media types supported by registered handlers and validate detected content after upload. ZIP extraction needs independent expansion/member limits.

Signed downloads are attachments. Source/PDF previews and generated page images need an explicit display flow. Decide its frontend CSP and object-store CORS configuration as part of that feature. Original file viewing and generated HTML/SVG should be controlled separately.

### Checkpoints and product data

Checkpoint tables record execution state. Notes, sources, evidence links, revisions and review decisions need their own product tables. A checkpoint ID cannot substitute for ownership checks or a readable source ledger.

### Caching

Provider cache-token accounting, cached prompt-file loading, and Next.js component caching already exist. Version-aware reuse of extracted pages, chunks, embeddings, generated sections and rendered documents still needs implementation. Cache keys must include relevant source hashes, extraction/chunking version, project/access scope, profile/prompt/model/schema versions, and template version. Keep force-regeneration and dependent-result invalidation explicit.

### Testing boundaries

The database test harness can downgrade and recreate its configured test database. Use a disposable database or testcontainers. Live evaluations intentionally require an explicit command and real credentials; ordinary checks replay/fake model calls. Keep real private course materials and private generated content out of the public repository and fixtures.

## 4. Proposed application modules

Preserve the generated `backend/src/app` package and add product modules around the existing infrastructure:

```text
app/
  api/                         Thin HTTP routes using existing /v1 router
  projects/                    Ownership, profiles, instructions, revisions
  sources/                     Registrations, versions, roles, upload workflow
  ingestion/
    registry.py                Implemented handler capabilities
    contracts.py               Extracted block and locator schemas
    handlers/                  PDF, transcript, text, code, notebook, ZIP
  knowledge/                   Notes, sections, revisions, claims, evidence
  search/                      Full-text and vector indexing/retrieval
  jobs/                        Durable commands, attempts, leases, cancellation
  generation/
    schemas.py                 Outlines, drafts, diagrams, findings, run state
    graph.py                   Note Foundry StateGraph and conditional paths
    stages/                    Small independently testable stage functions
  reviews/                     Coverage, support, clarity, technical checks
  rendering/                   SVG components, HTML/CSS, PDF, page audit
  mcp/                         Thin adapter to the same product services
  worker/                      Process entry point and resource lifecycle
  llm/                         Existing gateway: reuse and extend carefully
```

Use dependency injection to pass the gateway, sessions and storage to services. The worker needs the same engine/session/gateway/checkpoint/storage construction and cleanup as the API; extract reusable resource setup where warranted. Keep one long-lived async event loop per worker process, as the gateway's existing documentation requires.

## 5. Proposed data contracts

Start with project/profile revisions; sources/source versions and their project roles; extracted blocks and locators; notes/note versions; runs and stage attempts; artifacts; claims/evidence links; review findings/decisions. Add chunk/embedding records when indexing is introduced.

Required distinctions:

- A handler reads a format; a source role determines use; a profile determines output and checks.
- Allowed input types are optional choices, not a mandatory bundle.
- Original extraction, retrieval chunks, generated notes and review judgements are different data.
- Source/profile/note revisions are immutable inputs to a run. Manual edits create explicit revisions.
- Stable locators identify the original source version and page/region, timestamp range, line range, or notebook cell. Normalisation retains a mapping to original content.
- Persist embedding model/dimensions and content/chunking versions; do not mix incompatible vector spaces.
- Associate model call records with the application run and stage attempt. Prefer application-owned linkage/metadata over casually changing template-owned budget tables.

## 6. Proposed generation workflow

The graph handles the generation/review run over selected, registered source versions. Ingestion is independently resumable and reusable; it can be scheduled first when sources are not yet ready.

```text
Validate run configuration and source availability
    -> Build evidence/coverage outline from the selected source set
    -> Draft sections with supporting source references
    -> Plan and render useful diagrams
    -> Review support, completeness, technical accuracy and simplicity
       -> bounded section repair when findings are repairable
       -> review-needed pause when policy requires user input
       -> render document when content checks pass
    -> Render PDF pages and audit presentation
       -> bounded layout repair when appropriate
       -> review-needed state for unresolved findings
    -> Save final artifacts and findings for this note version
```

Suggested graph state holds IDs and compact status rather than duplicating an entire long transcript: `run_id`, `project_id`, selected source-version IDs, profile revision, outline/draft revision IDs, finding/artifact IDs, repair counters, and status. Associate the graph thread ID with the run and authorise every inspection or resume.

Use `gateway.model().with_structured_output(...)` for typed stage responses and propagate `gateway.call_config(...)` to preserve budget/feature/user/prompt metadata. Compile with `gateway.checkpointer`. All source/review/storage access goes through project-scoped services.

Design database writes and artifact creation to be safe on retries. A checkpoint does not imply that an external request ran exactly once. Avoid separate overlapping retry loops that multiply model calls; preserve the template's provider retry policy and make stage retries explicit. Job state tracks scheduling; graph state tracks workflow execution; note versions hold knowledge.

## 7. Proposed HTTP/UI/MCP surfaces

The initial web flows are project creation/settings, source import and inspection, run progress, note reading/editing, review with evidence, revision comparison, and artifact export. New routes should follow the current typed OpenAPI client pattern.

Illustrative API groups (proposed, not implemented):

| Group | Operations |
| --- | --- |
| Formats | List implemented handlers/capabilities |
| Projects | Create/list/read; version profile and permitted inputs |
| Sources | Initiate upload; complete/verify upload; assign role; inspect extraction/locators |
| Runs | Start; get progress/findings; request cancellation; submit authorised resume input |
| Notes | Read/edit versions; propose and compare generated revisions |
| Reviews | Read findings and source evidence; record feedback/decisions |
| Search | Query authorised source passages and notes, distinctly labelled |
| Artifacts | Preview derived pages and download version-specific outputs |

MCP should expose project profiles, source search/reads, note-version reads, and review/revision proposals through the same services. Development-time Next.js DevTools MCP already present in the base is a different capability.

## 8. Implementation order and acceptance checks

| Slice | Implementation | Checks that matter |
| --- | --- | --- |
| 1. Adopt foundations | Apply pinned base/FastAPI/Next.js refs to a branch, preserve design documents, set actual owner, regenerate API types as needed. | Full generated `make check`, migration round trip, container startup, and browser-to-API checks for the exact combination. |
| 2. Projects and input registry | Project/profile versions, ownership, allowed handler choices, source roles, dynamic creation form. | Empty project works; any supported combination works; disallowed formats fail clearly; cross-project reads/writes fail; changing settings preserves historical runs. |
| 3. Source import and inspection | Authorised signed uploads and completion verification; text/VTT/PDF handlers; locators, image-only detection and extraction status. | Text and timestamps survive; original pages open from a locator; unfinished/ambiguous extraction is visible; archive processing is bounded when ZIP is added. |
| 4. Worker and one concept | Durable job plus small StateGraph using existing gateway/checkpointer; source-backed draft, diagram and review schemas. | Fake-model CI; checkpoint/restart recovery; bounded repair; malformed/truncated output; budget exhaustion; owned pause/resume; no duplicate note/artifact commits. |
| 5. Visual proof and review | Six representative notebook pages, SVG diagrams, PDF export, page-image review, source/claim view and revision diffs. | Compare to supplied sample; no clipping; code operators copy correctly; diagrams agree with source/code; unsupported claims and missing topics are surfaced. |
| 6. Complete library capabilities | Full-session workflow, indexing, caches, search, MCP, more handlers. | Retrieval evaluation, permission-scoped search, stale-index/cache invalidation, agent findings visible in the UI, and a different project/input mixture. |
| 7. Remote use | Production identity hooks, managed DB/storage settings, operational limits, backups and restore procedure. | Production rejects local identity; data survives restart/redeploy; access restrictions hold; restore and interrupted-run recovery are demonstrated. |

A later small live evaluation should use an explicitly chosen model and budget to assess handwritten-page reading, simple explanations and diagram specifications. Existing generic gateway evals validate integration behaviour; they do not establish educational or presentation quality.

## Code evidence

- [Gateway and custom StateGraph guidance](https://github.com/olaheavy-dev/house-template-fastapi/blob/11398774187b2e54ae4a204624c1735b896e8e5f/template/backend/src/app/llm/gateway.py#L133)
- [Checkpoint pool and migrations](https://github.com/olaheavy-dev/house-template-fastapi/blob/11398774187b2e54ae4a204624c1735b896e8e5f/template/backend/src/app/llm/checkpoints.py#L57)
- [Agent/checkpoint tests](https://github.com/olaheavy-dev/house-template-fastapi/blob/11398774187b2e54ae4a204624c1735b896e8e5f/template/backend/tests/test_llm_agents.py)
- [Live structured-output and agent evaluations](https://github.com/olaheavy-dev/house-template-fastapi/blob/11398774187b2e54ae4a204624c1735b896e8e5f/template/backend/tests/evals/test_gateway_evals.py)
- [Prompt loader](https://github.com/olaheavy-dev/house-template-fastapi/blob/11398774187b2e54ae4a204624c1735b896e8e5f/template/backend/src/app/llm/prompts.py#L27)
- [Model-call data](https://github.com/olaheavy-dev/house-template-fastapi/blob/11398774187b2e54ae4a204624c1735b896e8e5f/template/backend/src/app/llm/budget.py#L113)
- [Media restrictions](https://github.com/olaheavy-dev/house-template-fastapi/blob/11398774187b2e54ae4a204624c1735b896e8e5f/template/backend/src/app/llm/budget.py#L361)
- [Storage helpers](https://github.com/olaheavy-dev/house-template-fastapi/blob/11398774187b2e54ae4a204624c1735b896e8e5f/template/backend/src/app/storage.py#L68)
- [Application router](https://github.com/olaheavy-dev/house-template-fastapi/blob/11398774187b2e54ae4a204624c1735b896e8e5f/template/backend/src/app/api/__init__.py#L29)
- [Frontend proxy timeout](https://github.com/olaheavy-dev/house-template-nextjs/blob/4e9d5717ec4c8215a9f0beebb7ce4c1ef90aa42a/template/frontend/src/lib/api/proxy.ts#L40)
- [Frontend page checks](https://github.com/olaheavy-dev/house-template-nextjs/blob/4e9d5717ec4c8215a9f0beebb7ce4c1ef90aa42a/template/frontend/e2e/pages.spec.ts#L7)
