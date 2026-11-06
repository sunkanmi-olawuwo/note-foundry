# Note Foundry: template suitability assessment

Assessed on 6 November 2026. **Recommendation: use the base, FastAPI, and Next.js Copier layers together as Note Foundry's foundation.** They provide substantial infrastructure and useful frontend/backend boundaries. The knowledge-bank product modules remain application work.

This assessment inspected source code and ran template checks in scratch copies. It did not change template or application runtime code, run paid model calls, deploy services, or implement product features. This document was subsequently incorporated into the repository as part of documentation consolidation.

## Inspected versions and verification

| Repository | Inspected commit | Local result |
| --- | --- | --- |
| `olaheavy-dev/house-template-base` | `7c056c804a568696f9498619417c9f22357c9ed3` | Rendered as the first layer |
| `olaheavy-dev/house-template-fastapi` | `11398774187b2e54ae4a204624c1735b896e8e5f` | 28 template tests passed |
| `olaheavy-dev/house-template-nextjs` | `4e9d5717ec4c8215a9f0beebb7ce4c1ef90aa42a` | 95 template tests passed |

Rendered all three inspected commits into a scratch project using `stacks: [fastapi, nextjs]`, `backend: fastapi`, Python 3.14, and the actual repository owner `sunkanmi-olawuwo`. Expected API, database, model-gateway, frontend-proxy, and pgvector migration files were present. `docker compose config --quiet` passed for the combined configuration. Copier reported shallow-clone warnings; the checks completed successfully.

The inspected main-branch commits have passing [base CI](https://github.com/olaheavy-dev/house-template-base/actions/runs/40570202406), [backend CI](https://github.com/olaheavy-dev/house-template-fastapi/actions/runs/40564276176), and [frontend CI](https://github.com/olaheavy-dev/house-template-nextjs/actions/runs/40564295316). The frontend run includes the successful full-stack FastAPI job, generated-project checks, and a template-rendering job.

Scope limitation: the frontend's full-stack CI pins base `v0.4.0` and FastAPI `v0.6.0`; it is not a full runtime test of the newest three inspected commits together. The combined local check validated generation and Compose configuration, not container startup, migrations against a live database, or the entire generated application's `make check`. Those checks belong in the adoption branch before merge.

Latest releases observed during review were base `v0.6.0`, FastAPI `v0.6.1`, and Next.js `v0.3.0`. Some inspected main commits are newer than those tags. Pin an explicit tested release or commit for each layer, then validate that exact combination.

## What is reusable

**Base layer.** Copier answers and ownership manifests, common Make targets, agent instructions, CI/security workflows, decision records, container-publishing scaffolding, and update machinery. Applying the base is a prerequisite for both stack layers. The base README still describes some implemented layers as planned, so the layer implementations and tests are stronger evidence than that summary wording.

**FastAPI layer.** An application factory and lifespan, async SQLAlchemy/psycopg, PostgreSQL with pgvector, Alembic with separate template/application migration branches, structured error responses and logging, health checks, telemetry hooks, pagination, optional S3-compatible storage, containers, and test infrastructure. Its LLM gateway includes budget reservations in PostgreSQL, usage records, prompt hashes, embedding calls, and LangGraph checkpoint integration. Product code can reuse these rather than introducing parallel model clients and database engines.

**Next.js layer.** Next.js App Router, React/TypeScript, design tokens, Tailwind, typed OpenAPI clients, a same-origin API proxy, sign-in integration hooks, production containers, telemetry, error pages, unit tests, accessibility checks, and screenshot tests. This is appropriate for the project library, note reader/editor, source inspection, and review workspace. Keep business rules and source access in FastAPI; use Next.js for UI and session-to-backend integration.

The inspected frontend requires Node 26 and pnpm 12. The backend generator defaults to Python 3.14, with 3.12 and 3.13 alternatives. Validate extraction/OCR dependencies against the selected Python version before pinning the product toolchain.

## Specific application additions and integration work

1. **Production identity and access.** Both authentication functions are deliberate stubs. FastAPI rejects protected production requests until real verification is implemented; Next.js must translate its real session into backend credentials. Add project ownership/membership checks, including on MCP access. A development user header is only a local/testing aid.
2. **Generic projects and source types.** Build the handler registry, selectable allowed input types, source roles, import sets, source versions, project instructions, and dynamic capabilities list. Existing storage upload settings allow common document types but need entries for the specific transcript, notebook, and code media types accepted by implemented handlers. Validate actual content and archive expansion as well as declared media type.
3. **PDF and handwriting processing.** The budget guard's `_UNBOUNDED_BLOCKS` includes `document`, `file`, and `input_file`, and raises `UnboundedMedia`. Image content blocks are handled separately. Consequently, a raw uploaded PDF is not directly usable as a model file input through the current gateway. The initial PDF handler should extract text, detect image-only pages, render appropriately sized page images, and submit bounded chunks through tested model inputs. Validate vision support and its cost accounting for the selected model. Extending raw-file support is a separate gateway change.
4. **Durable application jobs.** LangGraph checkpoints are useful for agent state but do not constitute the ingestion/generation job queue, scheduling, worker lifecycle, cancellation, lease/recovery, and duplicate-safe stage output system required here. Implement those as a distinct module and worker entry point, reusing database and gateway infrastructure.
5. **Project-specific prompts.** The template loads versioned prompt files and records their hashes. Add versioned project profiles, source-role instructions, and a composed-prompt manifest. Save the exact profile revision and selected source versions for each run. Tune the default 1,024-token output cap by stage; draft long documents in sections with explicit budgets.
6. **Knowledge, search, and review.** Add content blocks, evidence locators, notes/revisions, claims, review findings, coverage dispositions, full-text indexes, and embedding records. Enabling pgvector and providing an embeddings client does not create the product's indexing or retrieval pipeline. Keep source evidence distinguishable from generated notes during verification.
7. **Document rendering and previews.** Build the SVG diagram components, notebook-style HTML/CSS templates, Chromium PDF export, and rendered-page audits. Existing Playwright screenshot checks validate the application shell, not the generated PDFs. Add document-specific layout and code-copyability checks. Storage downloads are deliberately attachments; add controlled original-source/page preview endpoints or rendered images for side-by-side review. Configure storage CORS and the frontend's allowed connection/asset behaviour for the chosen preview/upload flow.
8. **Caching.** The gateway accounts for provider cache tokens and caches prompt-file reads; Next.js enables its component caching model. Neither supplies the version-aware source extraction, embedding reuse, generation-result reuse, or PDF-render cache discussed for Note Foundry. Add those explicitly with input hashes, prompt/profile/model/template versions, access scope, invalidation, retention, and force-regeneration.
9. **MCP product interface.** Base can configure Next.js DevTools MCP for development. This is separate from a Note Foundry MCP server exposing project-scoped search, source/note reads, and review/revision tools. Implement the latter as a thin adapter to the same application services as HTTP routes.

## Recommended next steps

1. Create a foundation branch in `sunkanmi-olawuwo/note-foundry`. Record the template-adoption decision and explicit source refs. Preserve the current README, architecture, roadmap, example profile, and quality contract; reconcile overlaps such as `.gitignore`, contribution conventions, and licensing deliberately.
2. Apply base, then FastAPI, then Next.js with separate Copier answers files. Set the repository owner explicitly to `sunkanmi-olawuwo`; the base defaults to `olaheavy-dev`. Choose both stacks and the FastAPI frontend backend. Generate from the official template repositories so future updates have useful source paths. Preserve template ownership boundaries instead of manually copying selected files.
3. Run the generated application's complete checks, migration round trip, image builds/startup, and frontend-to-API smoke test for that exact template combination. Regenerate OpenAPI types when needed. Production sign-in must have a clearly documented implementation plan before deployment.
4. Implement a small product slice: project creation, handler registry, flexible input selection, source import, and extraction preview with source references. Use synthetic test files and privately supplied course materials outside Git.
5. Add the job worker and one source-backed explanation/diagram. Build the six-page presentation proof and review workspace before scaling to complete class guides. Then add indexing, MCP, additional formats, and deployment incrementally.

No extra template is needed to get started. The existing two stack layers plus their base are a suitable foundation; the next work is integrating them carefully into Note Foundry and building its product behaviour.

## Implementation evidence

- [Base generator](https://github.com/olaheavy-dev/house-template-base/blob/7c056c804a568696f9498619417c9f22357c9ed3/copier.yml)
- [FastAPI template overview](https://github.com/olaheavy-dev/house-template-fastapi/blob/11398774187b2e54ae4a204624c1735b896e8e5f/README.md)
- [Backend authentication hook](https://github.com/olaheavy-dev/house-template-fastapi/blob/11398774187b2e54ae4a204624c1735b896e8e5f/template/backend/src/app/auth.py)
- [Media budget handling](https://github.com/olaheavy-dev/house-template-fastapi/blob/11398774187b2e54ae4a204624c1735b896e8e5f/template/backend/src/app/llm/budget.py)
- [Storage implementation](https://github.com/olaheavy-dev/house-template-fastapi/blob/11398774187b2e54ae4a204624c1735b896e8e5f/template/backend/src/app/storage.py)
- [Next.js template overview](https://github.com/olaheavy-dev/house-template-nextjs/blob/4e9d5717ec4c8215a9f0beebb7ce4c1ef90aa42a/README.md)
- [Frontend authentication hook](https://github.com/olaheavy-dev/house-template-nextjs/blob/4e9d5717ec4c8215a9f0beebb7ce4c1ef90aa42a/template/frontend/src/lib/auth.ts)
- [Full-stack CI workflow and version pins](https://github.com/olaheavy-dev/house-template-nextjs/blob/4e9d5717ec4c8215a9f0beebb7ce4c1ef90aa42a/.github/workflows/test.yml)
