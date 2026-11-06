# Architecture

## Decisions

Use a modular application with explicit frontend, interface, domain, and infrastructure boundaries. Start with PostgreSQL plus pgvector and file/object storage. Keep the web API and MCP adapter thin. Keep processing stages independent of HTTP requests and reusable by the worker and CLI tooling.

Proposed application structure:

```text
frontend/                         React + TypeScript browser application
backend/
  app/
    interfaces/http/              FastAPI routes and transport schemas
    interfaces/mcp/               Agent tools, resources, and prompt access
    modules/projects/             Profiles, instructions, project membership
    modules/sources/              Source records, versions, roles, import sets
    modules/ingestion/handlers/   Format detection and extraction
    modules/knowledge/            Notes, topics, revisions, evidence links
    modules/generation/           Outlining, writing, diagram planning
    modules/reviews/              Findings, coverage, review decisions
    modules/search/               Keyword and semantic retrieval
    modules/jobs/                 Checkpoints, leases, retry/cancel semantics
    infrastructure/llm/          Provider clients, structured output, usage
    infrastructure/database/     Persistence and migrations
    infrastructure/storage/      Filesystem/object-store adapters
    rendering/                    SVG diagrams and HTML/PDF production
    worker/                       Job execution entry point
  migrations/
  tests/
templates/                        Versioned document and diagram assets
examples/                         Non-sensitive configuration examples
docs/
deployment/
```

These paths are a proposed structure, not existing application modules.

## Frontend and API boundary

The frontend handles library navigation, project configuration, source uploads, source inspection, note editing, job progress, review findings, revision diffs, reading, and exports. It does not hold provider credentials or invoke LLMs directly.

The backend validates project access and allowed input types, persists commands and jobs, applies prompts, records immutable run inputs, and controls source and output versioning. Versioned API contracts should be described by OpenAPI and reflected in frontend types.

Long-running requests create a job and return its ID. Polling is sufficient initially for progress. Work survives browser closure and can resume or retry from stored checkpoints. Retry safety must be designed around duplicate-safe stage outputs, worker leases, timeouts, cancellation checkpoints, and partial failures. External LLM calls cannot be assumed to execute exactly once.

## Project profiles

A project contains a goal, audience, allowed input handlers, optional source-role expectations, free-text instructions, terminology, output preferences, template version, and review policy. Workflow requirements are explicit and conditional. Creating an empty project and adding sources later is valid.

Project settings are editable and versioned. A run snapshots the selected sources, source roles, source versions, profile revision, prompt versions, model configuration, extraction settings, schema version, and template version. A changed profile does not silently rewrite past outputs.

Use separate task prompts for extraction, outlining, drafting, diagram planning, and review. Combine each with the applicable project profile. Presentation layout lives in renderer templates. Imported content is evidence data; it cannot grant tools new permissions or override application instructions.

## Generic ingestion

Handlers advertise an ID, supported formats/media types, extraction capabilities, configuration schema, and availability. The interface obtains its list from the backend registry. Backend validation applies consistently to the browser, HTTP clients, and MCP tools.

Each handler returns structured blocks with stable IDs, original source/version IDs, type, text, source locator, extraction method, uncertainty flags, and optional image/asset references. Preserve format-specific information: page/region, timestamps, file/line ranges, or notebook cell/output identity.

PDF ingestion detects image-only pages and schedules OCR/vision processing. Code stays exact and is never reconstructed from a styled PDF when an original file is available. Saved notebook outputs are historical until independently rerun. ZIP ingestion treats archives as containers, enforces expansion limits, blocks path traversal, and dispatches supported members to handlers. Unsupported inputs produce clear per-file findings.

The teaching pipeline works with these blocks and source roles, independent of the original file format. Adding another handler should not require rewriting generation or review.

## Data and search

Core entities: projects and memberships; versioned profiles; sources and source versions; project/source associations and roles; content blocks; embeddings; notes and note versions; claims/evidence links; jobs, runs and stage attempts; review findings and decisions; artifacts and template versions.

Use relational columns and constraints for identity, access, relationships, statuses, and versions. Use validated JSONB for variable handler metadata, configuration, and structured note blocks. Original binaries and generated assets live in file/object storage with database references and hashes.

Index source blocks and generated notes separately. Use PostgreSQL full-text search plus pgvector retrieval when semantic search is enabled. Apply project/access restrictions to every retrieval. Record embedding provider/model/version, dimensions, text hash, and chunking strategy. Do not mix incompatible embedding spaces. Start with exact vector search and benchmark any approximate index against the exact baseline, including selective project filters.

Retrieval finds candidate evidence; review checks actual support. Coverage analysis considers the whole selected source set rather than only top search results. Citation pointers alone do not establish factual accuracy.

## LLM boundary and caching

A provider adapter exposes task-level requests and validated responses while recording usage, latency, failures, and model configuration. Provider-specific options remain in that adapter. Credentials live in server secrets. Define timeouts, retry limits, per-run budgets, and explicit handling of invalid, incomplete, or refused responses.

Cache extraction, page interpretations, embeddings, eligible generation results, and render outputs. Keys include the relevant source/content hashes, handler and prompt versions, project/access scope, model settings, schema, and renderer version. Cache hits must preserve provenance. Provide force-regeneration and invalidate dependent results when inputs change. Bound storage growth and define retention for transient entries. A separate cache service is an optional later optimisation.

## Rendering and review

Generate diagrams from checked specifications using reusable SVG components. Bind versioned note content and assets into HTML/CSS templates, then export PDF through Chromium/Playwright. The web reader and export share content and design tokens. The PDF is an artifact of a note revision, not the editable source of truth.

Review views pair notes with supporting sources and show coverage, technical checks, clarity findings, page previews, and revision diffs. Keep automated findings separate from human decisions. Generated changes become proposed versions and do not overwrite manual edits. Code execution is a separate controlled capability with explicit runtime limits and environment records.

## MCP boundary

Expose a small authenticated adapter to the same services used by the HTTP API. Initial capabilities: list projects, read project profiles, search sources, fetch source passages, read note versions, submit review findings, and propose revisions. Job-triggering tools can return a persisted job ID when added.

Use immutable note/source version IDs in review tools. Enforce project access and maintain an audit trail. The MCP adapter does not provide unrestricted database or shell access. An MCP client for external services is a separate optional capability; it is not needed to expose this knowledge bank to compatible agents.

## Local development and deployment

The foundation milestone should provide Docker Compose for the frontend, backend, worker, and PostgreSQL/pgvector, with durable local storage and repeatable migrations. Production uses configured file/object storage and a PostgreSQL service that supports pgvector. Include personal sign-in, server-enforced project access, secret injection, database/file backups, restoration verification, health checks, and structured logs before remote use.
