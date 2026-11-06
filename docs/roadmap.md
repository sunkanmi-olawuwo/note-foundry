# Roadmap

All milestones below are planned. The repository currently contains design documents and an example profile only.

## 1. Application foundation

Establish the frontend/backend/worker boundaries, dependency locks, linting, meaningful automated checks, Docker Compose, PostgreSQL with pgvector, migrations, storage adapter, and sample environment configuration. Add project ownership/access boundaries and a development-only local identity mechanism that is explicitly blocked in production.

Acceptance: documented startup brings up the stack; a migration creates the core project/profile/source/job schema; readiness checks include the database; a clean checkout can reproduce the setup. No LLM key is required for foundation checks.

## 2. Project creation and source inspection

Build the project creation flow with name, purpose, input types from the handler registry, audience, free-text instructions, output choices, and review settings. Add source roles and optional import sets. Implement text/Markdown, VTT/SRT, and PDF handlers first, including image-only page detection and an explicit unprocessed-page status.

Acceptance: a project can contain any permitted input combination or start empty; allowed types can be changed with clear treatment of existing sources; extracted passages link to original source locations; unsupported or uncertain extraction is visible rather than silently omitted.

## 3. One reviewed concept and visual proof

Add a provider adapter and versioned task prompts. Generate one beginner-friendly explanation from selected source versions with checked references, a suitable diagram, a small example, and an understanding question. Create six representative template pages: title/overview, concept explanation, diagram, code walkthrough, comparison, revision.

Acceptance: compare the rendered prototype against the user-supplied sample; inspect actual page images, readability, and code copyability. Distinguish source fidelity from technical accuracy. Store model usage and the full input/version manifest. Report uncertain claims. Preserve manual edits in a separate note revision.

## 4. Complete session workflow and quality workspace

Implement complete coverage analysis, section drafting, exercises/answers, code verification where applicable, revision notes, bounded repairs, and HTML/PDF export. Add side-by-side source review, page findings, coverage dispositions, and proposed revision diffs. Make jobs retryable from saved stages.

Acceptance: reconstruct the concurrency session from its original materials and review it against the sample. Evaluate another session with a different input mix. Check intentionally failing code, missing sources, poor handwriting, long code blocks, and crowded pages. Never infer verification success from a missing check.

## 5. Search and initial MCP integration

Add source/note full-text search and embeddings with project-filtered retrieval. Expose search, source/note reads, project-profile reads, review submissions, and proposed revisions through MCP. Reuse the application services and access checks.

Acceptance: a compatible agent can retrieve verifiable evidence and submit findings visible in the browser; source and note versions are explicit; an agent cannot cross its project permissions. Measure keyword and semantic retrieval against representative questions and exact vector-search baselines.

## 6. Deployable personal knowledge bank

Add production sign-in, storage configuration, deployment instructions, backups, restore checks, run budgets, retention controls, and operational visibility. Test restart recovery and incomplete external requests. Add additional formats only through the handler contract.

Acceptance: deployed data survives restarts and redeploys; only the intended account can access the library; failed work can be recovered; originals, notes, evidence, reviews, and artifacts can be exported. Document ongoing service and LLM usage costs using measured pilot data.
