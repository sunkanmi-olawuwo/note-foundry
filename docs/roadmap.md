# Roadmap

All milestones below are planned. The repository currently contains design documents and an example profile only. The [product brief](product-brief.md) records requirements R01-R22, the [decision register](decisions/README.md) distinguishes confirmed preferences from engineering recommendations, and the [implementation map](implementation-plan.md) gives code-level extension points. Resolve [open questions](open-questions.md) when each slice needs them.

## 1. Application foundation

Apply pinned base, FastAPI and Next.js template refs in that order, preserve this documentation, and record the exact Copier answers. Reuse their database, migrations, storage, LangChain gateway, LangGraph checkpointing, API connection and checks. Confirm the actual repository owner and reconcile file ownership and licence options. Plan reusable worker resource setup and production identity integration.

Acceptance: complete generated-application checks, migration round trip, container startup, frontend-to-API smoke test, and API-type consistency pass for the exact selected template combination. Setup is reproducible from a clean checkout. Local identity stays disabled in production. No live LLM call is needed for foundation checks.

## 2. Project creation and source inspection

Build project/profile and source-version models plus the creation flow with name, purpose, input types from the handler registry, audience, free-text instructions, output choices, and review settings. Add ownership, source roles, signed-upload initiation/completion and inspection routes. Implement the chosen first handlers, including image-only PDF detection and an explicit unprocessed-page status.

Acceptance: a project can contain any permitted input combination or start empty; allowed types can be changed with clear treatment of existing sources; extracted passages link to original source locations; unsupported or uncertain extraction is visible rather than silently omitted.

## 3. Worker and one reviewed concept

Build a durable job worker and a small custom LangGraph workflow using the existing model gateway and PostgreSQL checkpointer. Compose versioned project instructions with task prompts. Generate one beginner-friendly explanation from selected source versions with checked references, a suitable diagram specification, a small example, and an understanding question. Store note revisions, evidence and findings explicitly.

Acceptance: fake/replayed-model checks cover malformed or truncated output, bounded repair, budget exhaustion, checkpoint recovery, owned pause/resume and duplicate-safe outputs. Record model-call/run correlation and the input/version manifest. Preserve manual edits and distinguish unsupported statements from technical corrections. Run a small live quality evaluation only after model and budget choices are made.

## 4. Visual proof and quality workspace

Build six representative notebook-style pages: title/overview, concept explanation, diagram, code walkthrough, comparison, and revision. Implement SVG components, HTML/CSS, Chromium PDF export and rendered-page inspection. Add side-by-side source review, page findings, coverage dispositions, editing and proposed revision diffs. Handle controlled source/page previews explicitly.

Acceptance: compare the prototype against the privately supplied sample at readable resolution. No clipping, overlaps, missing glyphs or broken references; code operators copy correctly; diagram labels/arrows agree with the material. Calibrate density and minimum text sizes. Existing shell screenshots do not substitute for document checks.

## 5. Complete document workflow

Extend to complete coverage analysis, section drafting, exercises/answers, applicable code verification, revision notes, bounded repairs and document export. Use one output per class session in the course profile, while keeping other projects flexible. Apply project-specific review policies rather than requiring every stage for every document.

Acceptance: reconstruct the concurrency session from original materials and compare it with the sample; evaluate a different session or project with another input mix. Cover missing sources, unclear handwriting, long code, intentional lesson errors and unavailable dependencies. Record unresolved findings explicitly and preserve historical versions.

## 6. Search, caching and product MCP

Add source/note full-text search and project-filtered embeddings through the existing budgeted client. Implement version-aware processing-result reuse and invalidation. Expose project profiles, source/note reads, search, review findings and revision proposals through a thin MCP adapter to shared services.

Acceptance: benchmark retrieval, including selective project filters; preserve evidence locators and distinguish originals from generated notes. Source/profile/model/template changes invalidate the correct derived results. A compatible agent can submit findings visible in the browser, with ownership and immutable versions enforced. The Next.js development MCP remains a separate integration.

## 7. Deployable personal knowledge bank

Add production sign-in, storage configuration, deployment instructions, backups, restore checks, run budgets, retention controls, and operational visibility. Test restart recovery and incomplete external requests. Add additional formats only through the handler contract.

Acceptance: deployed data survives restarts and redeploys; only the intended account can access the library; failed work can be recovered; originals, notes, evidence, reviews, and artifacts can be exported. Document ongoing service and LLM usage costs using measured pilot data.
