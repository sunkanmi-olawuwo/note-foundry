# Product brief

## Purpose

Note Foundry is a personal knowledge bank that turns supplied source material into clear, traceable explanations and polished documents. It began with a need for class notes and should also work for research, books, software documentation, and personal learning projects.

The initial user priorities are equally important: simple explanations, source accuracy and completeness, necessary diagrams, beautiful presentation, and an application that can later be deployed and accessed by compatible coding agents.

## Initial pilot

The first collection is a privately supplied academy course. The confirmed output unit is **one document per class session**, rather than a combined weekly document. This is a course-profile choice, not a universal rule for every project.

The reference is a 35-page notebook-style concurrency study guide. An exploratory review identified ruled paper and red margins, Caveat/Kalam handwriting fonts, JetBrains Mono code, semantic colours, diagrams, code walkthroughs, exercises, a revision sheet, and answers. The source session includes a timestamped transcript, handwritten PDFs, code, commands, and an assignment. All nine instructor-notes PDFs inspected had no extractable text, making image-only detection and page-image reading important.

Use the reference privately as a design and coverage target. Exact reconstruction from PDF alone is not promised. The first visual proof should cover six representative pages: title/overview, concept explanation, diagram, code walkthrough, comparison, and revision. Calibrate readability and code copyability before generating complete sessions. Document length should follow the material, not a fixed 35-page requirement.

## Requirements

The IDs below provide a stable way to refer to the discussion. They describe intended behaviour; application implementation has not started.

| ID | Requirement | Main implementation area |
| --- | --- | --- |
| R01 | Organise knowledge in independent projects with a goal, audience, instructions, and output preferences. | Projects and profiles |
| R02 | On creation, list formats from the implemented handler registry and let the user choose any combination. Selected formats are allowed inputs, not a requirement to provide every type. | Project creation and ingestion |
| R03 | Allow an empty project and later imports; preserve history when project settings change. The policy for existing sources after a type is disabled remains to be chosen. | Profiles and source versions |
| R04 | Give each source a role: evidence, style reference, requirements, background, or a draft to improve. | Sources |
| R05 | Read supported formats generically into blocks with source-version IDs and locators; retain page/region, timestamp, line, or cell information. | File handlers |
| R06 | Detect handwritten/image-only pages and expose uncertain or incomplete extraction. | PDF/vision ingestion |
| R07 | Combine stable task prompts with versioned project instructions and terminology; record the exact inputs used for each run. | Prompt composition |
| R08 | Explain concepts in plain language, introduce prerequisites and jargon in order, use small examples, and check understanding when appropriate. | Generation and clarity review |
| R09 | Plan diagrams from the lesson/source content and code, select an appropriate visual form, and verify labels, arrows, timing and meaning. | Diagram planning and review |
| R10 | Separate the editable structured note from its rendered HTML/PDF exports. | Knowledge and rendering |
| R11 | Preserve the reference's useful design patterns through reusable components, sharp diagrams, and versioned templates. | SVG/HTML/CSS/PDF rendering |
| R12 | Check source support and technical correctness separately; label supplements and corrections, and preserve source disagreements. | Content review |
| R13 | Check coverage against the whole selected source set, not only retrieved top matches. Give important source items a reviewed disposition. | Coverage review |
| R14 | Verify runnable code where appropriate, distinguishing expected errors, historical output, illustrative output, invariants, timings, and unverified dependencies. | Code verification |
| R15 | Audit final page images for readability, layout, diagrams, references, and code copyability. | Presentation review |
| R16 | Provide side-by-side note/source review, findings by severity, revision diffs, direct editing and section regeneration. Preserve manual edits. | Review workspace |
| R17 | Save jobs and workflow state so generation can survive browser closure and recover after interrupted execution. Bound repair loops and make uncertainty visible. | Worker and LangGraph workflow |
| R18 | Index original source passages and generated notes distinctly; provide project-scoped keyword and semantic search with verifiable source locations. | PostgreSQL/pgvector search |
| R19 | Reuse extraction, embedding, generation and rendering results when their relevant inputs match. Support invalidation and explicit regeneration. | Application caching |
| R20 | Expose project-scoped search, source/note reads, and review/revision proposals through MCP using the same services as the web API. | Product MCP interface |
| R21 | Keep public source code separate from private inputs, generated private notes, credentials, databases and runtime artifacts. | Access, storage and repository hygiene |
| R22 | Support local development and eventual deployment with durable storage, sign-in, backups and operational visibility. | Deployment |

## Simple teaching pattern

For beginner projects, use a short definition, a concrete reason the concept is useful, a helpful diagram when needed, a small worked example, a common mistake, and an understanding question with an explanation. Adapt this structure to the topic and project. Avoid a universal mandatory chapter template.

An example style is: explain that multiple requests can make progress while waiting for responses, then introduce the word concurrency. Qualify the limits of the example before turning a shorthand into a technical rule.

## Diagram production

The LLM proposes what needs a visual, its form, labels, relationships, caption, and evidence. Application renderers turn the checked specification into consistent SVG. Review semantic accuracy and rendered appearance separately. The initial visual repertoire is timelines, sequence diagrams, decision flowcharts, concept maps, and comparison tables; implementation and prioritisation remain part of the visual pilot.

## Reading and reviewing

The intended interface includes a library, project setup, source import/inspection, run progress, a note reader/editor, review findings with supporting passages, revision comparison, search, and export. Users should be able to read notes without repeatedly invoking a model. A retrieval or reviewer score is evidence to inspect, not a guarantee of correctness.

## Scope boundaries

The core workflow is controlled generation and review. Open-ended autonomous research, a team of agents, a separate cache service, additional transcription formats, automated folder discovery, and organisation-wide collaboration are optional later capabilities. Exposing an MCP server does not require making the application's core workflow autonomous.

See the [decision register](decisions/README.md), [roadmap](roadmap.md), [quality contract](quality-contract.md), and [open questions](open-questions.md).
