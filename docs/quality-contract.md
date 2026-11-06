# Quality and review contract

These are acceptance criteria to implement and calibrate, not guarantees of automated correctness. Apply the dimensions relevant to a project's purpose, sources and review policy. See the [product brief](product-brief.md) for requirements and the [implementation map](implementation-plan.md) for the template integration.

## Content

- Each material factual or teaching claim has checked evidence or a clear supplement/correction label.
- Locators resolve to the source version used for the run. Check passage support as well as locator validity.
- A source-derived checklist records each essential topic, example, question, and assignment as covered, merged, excluded with a reason, or unresolved.
- Preserve disagreements between sources and distinguish the instructor's explanation from an externally supported correction.
- Match the project's purpose: a research synthesis, class guide, and code walkthrough may require different structures and reviews.

## Simplicity

For beginner projects, explain the problem before the mechanism, introduce one idea at a time, define terms before using them, start with a small example, and state the limits of an analogy. Use diagrams where they help. Remove repetition that adds no learning value. Ensure exercises are answerable from the notes and provide explained answers when requested.

## Diagrams

Validate labels, arrows, timing/order, branches, and captions against the supporting sources and code. Visual appeal is a separate check from semantic accuracy. Retain the diagram specification and rendered SVG with the note version.

## Code

Preserve exact original code where supplied. Mark adaptations, snippets with omissions, illustrative output, historical output, and newly observed results distinctly. Record interpreter/build, platform, dependencies, exit status, output and runtime constraints for executions. Match invariants for concurrency rather than exact print order or timing. Expected lesson errors and unavailable dependencies are explicit outcomes.

## Presentation

Use versioned design tokens and components. Check minimum text size, spacing, contrast, code copyability, clipped text, overlaps, glyphs, diagrams, tables, page references, headers/footers and unintended blank pages. Render final PDF pages and inspect at readable resolution. Different content should not be forced into the sample's exact page count.

Calibrate the course template with six representative sample-style pages before expanding to full sessions. Exact visual equivalence is unproven until compared. Preserve original code as canonical and verify PDF copy/paste because font rendering can look correct while extracted operators are corrupted. Application accessibility/screenshot tests and document-page checks cover separate outputs.

## Review experience

The workspace shows the note, supporting passages, coverage checklist, code results, rendered pages, and revision diffs. Findings contain severity, review dimension, note/claim/page identifier, evidence, suggested repair, status, and reviewer identity/type. Keep automated findings and human decisions distinct. A high average score must not hide an unresolved serious issue.

Users can edit, comment, accept a proposed revision, or regenerate a section. Preserve existing manual edits. Re-run affected content and rendering checks when changes invalidate earlier findings. Stop bounded repair loops with a visible review-needed state rather than silently claiming completion.
