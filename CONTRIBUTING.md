# Contributing

Start with the architecture, roadmap, and quality contract. The current repository is a design starter; implement features in small changes tied to a milestone and report actual capabilities accurately.

- Use [the documentation index](docs/README.md) to find the product requirements, discussion record, decisions, open questions, template assessment and implementation map. Keep them consistent when a decision or capability changes; distinguish confirmed preferences, recommendations, implementation and verification results.
- Keep domain/application logic independent of HTTP, MCP, and provider-specific clients.
- Use the handler registry for format support and preserve source/version locators throughout processing.
- Keep project selection flexible. Allowed file types do not imply that every selected type must be supplied.
- Add meaningful checks for extraction fidelity, access boundaries, durable processing, source-supported generation, and rendering changes. Use synthetic fixtures committed to the repository.
- Never commit credentials, private course materials, original user documents, extracted private passages, generated private notes, databases, logs, or uploads.
- Keep secrets in local ignored configuration or hosting secrets. Provide placeholder-only example configuration when runtime configuration is implemented.
- Record migrations and lock dependency versions. Document reproducible commands when runnable components are introduced.
- Describe what changed, how it was verified, and any unresolved limitations. Do not label a proposed or unavailable check as passed.

No open-source license has been selected. Keep licensing decisions explicit before distributing this project publicly.
