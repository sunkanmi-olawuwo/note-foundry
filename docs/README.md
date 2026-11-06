# Documentation

Updated 6 November 2026. This is the structured record of the project discussion, requirements, engineering recommendations, investigation findings, and remaining decisions. It is a synthesis, not a verbatim chat transcript. Credentials, private source material, and machine-specific paths are excluded.

## Current status

- Project name: **Note Foundry**; repository: **note-foundry**.
- Source repository: public, with a local checkout in the owner's portfolio workspace.
- Application: design stage. The templates have been inspected and composed in a scratch project; they have not been applied here.
- Existing template capabilities include LangChain, LangGraph PostgreSQL checkpointing, a budgeted model gateway, embeddings, optional LangSmith integration, storage, and frontend/API infrastructure.
- Template verification: 28 backend-template tests and 95 frontend-template tests passed in the recorded assessment. Combined rendering and Compose configuration validation passed. Full application checks for the exact template combination and product-quality evaluations remain work to do.
- Developer integration: the Dometrain MCP connection was configured outside this repository and a read-only course-list request succeeded. This is separate from the planned Note Foundry MCP server.

## Reading order

| Document | Purpose |
| --- | --- |
| [Product brief](product-brief.md) | User goals, requirements, examples, and the initial pilot |
| [Discussion record](discussion-record.md) | How the conversation's topics map to the maintained documents |
| [Decision register](decisions/README.md) | Confirmed preferences, recommended directions, superseded suggestions, and status |
| [Architecture](architecture.md) | System boundaries, modules, data, workflows, and integrations |
| [Template assessment](research/template-assessment.md) | Evidence that the existing templates are suitable and the limits of verification |
| [Implementation map](implementation-plan.md) | Code-level reuse points, missing capabilities, API/UI needs, and acceptance checks |
| [Roadmap](roadmap.md) | The implementation sequence and completion criteria |
| [Quality contract](quality-contract.md) | Source fidelity, correctness, simplicity, diagrams, code, and presentation |
| [Open questions](open-questions.md) | Choices that are not yet settled |
| [Example profile](../examples/project-profile.json) | Illustrative configuration, not implemented handler support |
| [Contributing](../CONTRIBUTING.md) | Public-repository and development conventions |

## Maintaining the record

Update the relevant requirement, decision, roadmap item, and evidence when the direction changes. Record whether something is a preference, recommendation, implemented capability, or verified result. Do not infer that a suggested dependency, provider, host, licence, or budget has been selected. Add task-specific implementation checks as features are built.
