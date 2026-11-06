# Open questions

These choices have not been settled by the discussion. They should be resolved when their implementation slice needs them, using a concrete proposal and evidence.

| ID | Choice | Needed by | Relevant consideration |
| --- | --- | --- | --- |
| Q01 | Exact base/FastAPI/Next.js release or commit combination | Template adoption | Pin explicit refs and validate the generated application together; prior full-stack CI used older base/backend pins. |
| Q02 | Production sign-in provider and session/token flow | Before remote access | Both template authentication hooks are intentionally unfinished. Retain server-enforced project ownership/access. |
| Q03 | Initial chat/vision and embedding configuration | First live generation evaluation | Measure handwritten-page extraction, plain explanations, structured outputs, latency and actual usage; provider/model defaults are not a product selection. |
| Q04 | Live evaluation and ongoing run budgets | Before paid calls | Reuse existing global/user caps; decide per-run limits and task sizes based on a pilot. No budget amount has been approved here. |
| Q05 | Hosting and production object-storage provider | Deployment | Templates support containers and S3-compatible storage. No hosting account, paid plan or provider has been selected. |
| Q06 | Source-code licence | Template adoption/public distribution | Public visibility is confirmed. No open-source licence has been selected; reconcile the base generator's licence option deliberately. |
| Q07 | First implemented format subset and library choices | Ingestion slice | Proposed formats include PDF, VTT/SRT, text/Markdown, Python, notebooks and ZIP. Only implemented/tested handlers should appear as available. Check extraction dependencies against the chosen Python version. |
| Q08 | Disabling a format after files exist | Project settings | Keep historical runs reproducible; decide how existing sources can be read, reused or replaced after the change. |
| Q09 | Scope and ownership of reusable sources across projects | Source/index schema | Define project links, roles, visibility and cache reuse without leaking private passages. Initial version can keep sources project-scoped. |
| Q10 | Review policy and calibrated thresholds | Generation/review slice | Decide when findings trigger bounded repair, a review-needed pause or completion. Serious unresolved issues should not disappear behind averages. |
| Q11 | Final template measurements and visual-match target | Six-page visual proof | Confirm fonts/assets, minimum text sizes, page density, code copyability and representative diagram layouts against the privately supplied reference. |
| Q12 | Controlled source/PDF preview flow | Review workspace | Storage helpers download attachments. Decide how page images and original-source views are delivered under existing CSP/access rules. |
| Q13 | Code verification environment and limits | Code checks | Distinguish harmless lesson examples, intentional errors, filesystem/network dependencies and unavailable services. Isolate executions and record the environment. |
| Q14 | Retention, deletion, backups and restoration policy | Before deployment | Cover original files, notes, graph checkpoints, embeddings, cached results and artifacts, including dependent invalidation. |
| Q15 | Initial MCP write capabilities | MCP slice | Begin with bounded search/read/review operations; decide which generation/revision commands are exposed and how results are reviewed. |

These are implementation choices, not reasons to block independent foundation work. See the [implementation plan](implementation-plan.md) and [roadmap](roadmap.md).
