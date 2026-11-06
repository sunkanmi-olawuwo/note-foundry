# Structured discussion record

Recorded from the project conversation through 6 November 2026. This records project-relevant substance and outcomes rather than reproducing chat messages. Private materials, credentials and local paths are not included.

| Discussion | Outcome and status | Maintained location |
| --- | --- | --- |
| Generate notes from academy session folders | Initial use case; inspect transcripts, archived code/notebooks, assignments and handwritten PDFs. Broaden the product to a reusable knowledge bank. | [Product brief](product-brief.md) |
| Weekly or per-session documents | User selected one document per class session. Other projects choose their own output scope. | [Product brief](product-brief.md) |
| Match the sample's style exactly | Aim to reproduce its notebook visual language and improve readability. Validate a six-page prototype before claiming a match; exact pagination/pixel equivalence is unproven. | [Quality contract](quality-contract.md) |
| Plan necessary diagrams | LLM examines source text, page images and code; proposes an evidence-linked specification; a renderer controls appearance; reviewers check meaning and layout. | [Product brief](product-brief.md) |
| Make explanations easy to grasp | Beginner clarity is a quality requirement: prerequisites, plain definitions, small examples, diagrams when useful, mistakes, and explained questions. | [Quality contract](quality-contract.md) |
| How the application is invoked and displayed | Browser interface for library, creation/import, reading/editing, progress and review; backend owns processing and LLM credentials. | [Architecture](architecture.md) |
| Generic knowledge bank and deployment | Projects, sources, notes, topics, revisions and evidence; editable notes with document exports; deployable personal application. Hosting is still undecided. | [Architecture](architecture.md) |
| Proper frontend/backend modules | Use explicit boundaries and shared services for web and MCP. The existing Next.js and FastAPI templates are the recommended starting point. | [Implementation map](implementation-plan.md) |
| Different document sets per project | Supported type selection comes from the handler registry. Any permitted combination or no files initially is valid. Source role and project instruction are separate from file type. | [Product brief](product-brief.md) |
| Review and indexing | Index blocks with locators and versions. Review claim support, whole-source coverage, correctness, simplicity, code and rendered presentation. UI pairs output with evidence. | [Architecture](architecture.md) |
| Agent access through MCP | Product MCP adapter exposes scoped knowledge/review services. It is distinct from an MCP client used to consume external tools. | [Architecture](architecture.md) |
| PostgreSQL instead of SQLite | User requested PostgreSQL from the start with pgvector; use it for relational data, full-text/vector search, jobs and processing results. SQLite prototype suggestion is superseded. | [Decision register](decisions/README.md) |
| Caching | Reuse expensive results with version-aware keys and invalidation. Redis or another separate cache is optional and unselected. | [Architecture](architecture.md) |
| Which component outputs documents | Backend rendering combines checked content and SVG with HTML/CSS and exports through Chromium/Playwright. The LLM supplies content/specifications. | [Architecture](architecture.md) |
| Naming and repository | User selected `note-foundry`. GitHub repository was created, starter documents committed/pushed, and local clone moved to the portfolio workspace. | [Documentation index](README.md) |
| Repository visibility | Initially private; user explicitly requested public and the change was verified. This does not make application data public. Licence remains undecided. | [Decision register](decisions/README.md) |
| Template suitability | Base, FastAPI and Next.js source inspected; 123 template tests, scratch composition and Compose validation passed. Full runtime validation of the selected combination remains necessary. | [Template assessment](research/template-assessment.md) |
| LangChain/LangGraph availability | Both already exist in the FastAPI template. Reuse its gateway, budgets, embeddings, agent helper and PostgreSQL checkpointing. | [Implementation map](implementation-plan.md) |
| Whether autonomous agents are necessary | Core generation can be an explicit LangGraph workflow with conditional stages and bounded repairs. Autonomous research/tool selection is an optional later capability. | [Decision register](decisions/README.md) |
| Deeper template investigation | Documented exact extension points, raw-file budget restrictions, prompt composition, output limits, job/worker requirements, storage preview gaps and API/UI work. | [Implementation map](implementation-plan.md) |
| Dometrain MCP setup | Registered and authenticated as a developer-side MCP connection; a read-only course-list request succeeded. Key entry and host launch details stay outside the repository. It is not the product's MCP server. | [Documentation index](README.md) |
| Capture the conversation in the repository | Consolidate product requirements, decisions, architecture, assessments, open questions and roadmap; reconcile superseded suggestions and clearly label unimplemented work. | This record and [documentation index](README.md) |

## Corrections carried forward

- Next.js is the frontend direction, replacing the early Streamlit dashboard suggestion for this broader application.
- PostgreSQL plus pgvector replaces the earlier SQLite prototype recommendation.
- LangChain, LangGraph and optional LangSmith are template capabilities to reuse, not new integrations to establish independently.
- Checkpointing does not by itself implement the application's job queue, project permissions, source ledger or review workspace.
- Existing frontend screenshot checks are not evidence that generated PDFs match the reference.
- Configuring a developer MCP connection does not implement the Note Foundry MCP service.
- The application remains a design starter until template adoption and feature implementation happen.
