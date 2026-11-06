# Decision register

Updated 6 November 2026. **Confirmed** means explicitly selected or requested by the owner. **Recommended** means the discussion produced an engineering direction that still needs an implementation decision and validation. Neither status implies implementation is complete.

| ID | Topic | Direction | Status |
| --- | --- | --- | --- |
| D01 | Name | Note Foundry; repository slug `note-foundry`. | Confirmed; repository created |
| D02 | Source visibility | Public GitHub repository. Keep private source materials, notes, credentials and runtime data outside Git. | Confirmed; visibility changed and verified |
| D03 | Course output unit | One document per class session for the academy project. | Confirmed preference; feature planned |
| D04 | Project inputs | Show implemented document formats on creation; users choose allowed types. Projects do not require a universal document bundle. | Confirmed preference; feature planned |
| D05 | Primary storage | PostgreSQL with pgvector from the start. Keep original binaries and generated files in file/object storage. | Confirmed database direction; feature planned |
| D06 | Application stack | Next.js frontend and FastAPI backend with a worker and shared services. Reuse base/FastAPI/Next.js Copier templates in one repository. | Recommended after source/test investigation; adoption pending |
| D07 | LLM foundation | Use the existing template LangChain gateway, spending controls, embeddings, LangGraph checkpointing and optional LangSmith support. | Existing template capability; reuse recommended |
| D08 | Orchestration | A custom controlled LangGraph workflow for generation, review, bounded repair and optional review pauses. Keep job scheduling and product data explicit. | Recommended; graph and worker not implemented |
| D09 | Prompts | Versioned project profile plus task prompts; snapshot the final composed instructions and source versions per run. | Recommended; configuration example only |
| D10 | Evidence and indexing | Source blocks retain original locators and versions. Search sources and notes distinctly; verify claims against originals and coverage against the selected source set. | Recommended; feature planned |
| D11 | Presentation | Checked diagram specifications rendered as SVG; versioned HTML/CSS and Chromium/Playwright PDF export. Calibrate against six representative sample-style pages. | Recommended; renderer not implemented |
| D12 | Quality | Separate source fidelity, completeness, technical accuracy, beginner clarity, diagram semantics and presentation checks. Preserve manual edits and expose unresolved findings. | User priorities; contract documented, checks planned |
| D13 | Agent interface | Add a project-scoped MCP adapter to the same services as HTTP. An external MCP client is a separate optional capability. | Recommended; product interface not implemented |
| D14 | Caching | Initially store reusable processing results in the database/file store with appropriate keys and invalidation. Introduce a separate cache only when justified. | Recommended; separate cache product unselected |

## Superseded suggestions

- **SQLite for a small local prototype** is superseded by D05.
- **Streamlit as the main interface** is superseded by the dedicated Next.js/FastAPI direction in D06.
- **A generic new provider adapter** is refined by D07: the existing template gateway is the integration boundary to reuse.

## Not yet decided

Authentication provider, exact template refs to adopt, production hosting/storage, initial LLM/vision models, live-evaluation budget, licence, initial implemented format subset, and the policy for existing files when a format is disabled are tracked in [open questions](../open-questions.md).

When a substantial recommendation is accepted or changed during implementation, add a numbered architecture decision record under this directory and update this register. Preserve the reasoning and affected requirements; do not turn an old recommendation into a claimed completed feature.
