---
name: project-html-architect
description: Use when the user asks to read or study a code project and turn it into architect/interviewer-oriented learning material, first-principles analysis, HTML pages with diagrams/tables/text, or resume/project-experience wording. This skill is especially relevant when the user wants a SOP for reading codebases, says CodeGraph exists, asks what technical points are differentiating, or wants separate pages per module such as control plane, reader, writer, or data plane.
---

# Project HTML Architect

Use this skill to read a real code project and produce high-signal technical learning material. The usual output is an HTML page under `html/<project>/...`, plus optional resume wording. The page language should follow the user's request; the skill instructions stay in English.

The standard is to explain the system in terms a senior architect or interviewer can evaluate without already knowing the internal business.

## Operating Contract

Before writing conclusions, establish the contract:

- Goal: learn the project and explain it from first principles.
- Audience: architect/interviewer who cares about engineering depth, scale, and general mechanisms.
- Scope: focus on the project or module the user named; split pages by module when requested.
- Source of truth: user-supplied differentiating points first, then CodeGraph, code, docs, logs, and runtime examples.
- Output: HTML with text, diagrams, and tables; optionally resume bullets.
- Boundary: do not invent scale numbers, internal meanings, or business claims.

If the user has already supplied the differentiating points, do not replace them with your own priority list. Use the user's points as the outline, then read code to explain and verify them.

## Reading The Project

Use this order every time.

### 1. Locate The Project And Index

1. Confirm the project root and whether `.codegraph/` exists.
2. If `.codegraph/` exists, use CodeGraph before broad grep:
   - Prefer MCP `codegraph_explore` / `codegraph_node` if available.
   - Otherwise try shell `codegraph explore "<question>"` and `codegraph node <symbol-or-file>`.
   - If CodeGraph CLI/tools are unavailable, say so briefly and fall back to `grep`, `find`, and targeted file reads.
3. Read local `AGENTS.md`, `README.md`, docs, API schemas, and `.trae/specs` or `.trae/documents` before deep code walks.
4. Identify entrypoints, module boundaries, storage, queues, RPC/HTTP contracts, background jobs, and generated-code boundaries.

Completion criterion: you can name the module's role, its neighboring systems, and the 3-6 files or packages that implement the important mechanisms.

### 2. Extract General Engineering Mechanisms

Prefer mechanisms that are recognizable outside the company:

- Storage model: LSM tree, wide-column storage, object storage, HDFS, Redis, DB.
- Consistency model: idempotency, at-least-once delivery, Saga, state machine, optimistic lock, reconciliation.
- Indexing/query model: GSI, covering index, cursor pagination, backfill, rebuild.
- Scale model: sharding, bucketing, batching, streaming, external sort, backpressure.
- Data processing: Spark, MQ, manifest expansion, small-object packing, range reads.
- Control plane: task orchestration, leases, heartbeats, barriers, route registration.
- Reliability: retries, exponential backoff, dead letter, compensation, audit/reconciliation.
- Security: server-side authority mapping, structured ID, auth boundary, cache poisoning risks.

Avoid undifferentiated or business-heavy points unless the user asks for them:

- Generic metadata modeling.
- CRUD mappings that only project participants care about.
- Object-to-object mapping details with no transferable technical depth.
- Internal product names without translating their essence.

Completion criterion: every selected point can be explained as a reusable architecture pattern, not only as a local business workflow.

### 3. Validate Claims With Evidence

For every claim that sounds factual, find evidence:

- code constants and config defaults for concurrency, bucket counts, batch sizes, timeouts
- docs or API schemas for request/response fields and source types
- code paths for retries, idempotency, state transitions, route registration, backpressure
- runtime examples for throughput, durations, rows, bytes, reader/writer counts

When using metrics:

- Compute elapsed time from timestamps.
- Compute throughput only when both numerator and denominator exist.
- Distinguish object inventory/listing bytes from real media-object payload bytes.
- Do not convert "URI records processed" into "PB media processed" unless the input actually proves it.

Completion criterion: the material separates facts, computed metrics, and assumptions.

## HTML Output Requirements

Create or update one HTML file per project/module under repo-root `html/<project>/`.

Use `templates/html-page.html` as the starting point when creating a new page. Replace all placeholder tokens and add/remove sections as needed, while keeping the page self-contained.

Use this structure unless the user requests a different one:

1. Header: project/module name and one concise thesis.
2. Sections: each major technical mechanism gets a section.
3. For each section:
   - first-principles question or thesis
   - concise explanation paragraphs
   - a diagram using inline SVG or structured HTML
   - a table row or summary that names the problem, mechanism, and engineering gain
4. Final synthesis block with a neutral title such as "Core Conclusion", "Technical Takeaways", or "Project Value". Translate the title to the output language when appropriate.

HTML standards:

- Include real text. Do not create diagram-only pages.
- Include diagrams and tables for software-engineering concepts.
- Do not use visible section titles that sound like prompt artifacts, such as "interview expression" or "resume expression". Use neutral product/document titles instead.
- Keep pages self-contained: inline CSS, no external asset dependency unless the user explicitly provides one.
- Use restrained, readable design; avoid marketing hero pages.
- Put each project in its own folder, for example `html/compound/...` or `html/general_console/...`.
- Validate with `python3 -m html.parser <file>`.

When explaining internal technologies, translate them:

- `Fuxi` -> wide-column or LSM-tree style storage if that is the underlying mechanism in context.
- `TOS/OSS` -> object storage.
- `VDA` -> Video Data Access when local docs confirm it; explain it as the video data source or video metadata/access side.
- Internal queues/RPC frameworks -> message queue / RPC framework unless the brand matters.

## Module Splitting

If the project has distinct planes or runtime roles, split them:

- Control Plane: job lifecycle, state model, scheduling, leases, heartbeats, barriers, route registration.
- Reader: input normalization, file discovery, manifest expansion, parsing, hash bucketing, batching, sink/backpressure.
- Writer: endpoint registration, idempotent batch ingestion, buffering, spill, external sort, merge, final commit.
- Data Plane: authoritative storage, idempotent writes, async index/event path, consistency, reconciliation.

Do not merge split modules into one end-to-end page when the user asked for separate Reader / Writer / Control Plane pages.

## Resume Wording

When the user asks for resume wording:

1. Start with the project purpose in one sentence.
2. Use ownership language, equivalent to "responsible for landing/implementing", adapted to the output language.
3. Split bullets by architectural responsibility, not by file names.
4. Include mechanisms and measurable scale only when verified.
5. Avoid unsupported claims like "processed PB-scale media in minutes" unless the data proves it.
6. Provide LaTeX if the user is writing a LaTeX resume.

Good shape:

```latex
\item \textbf{Project Name:}
One sentence describing the project goal, data object, and system value.

\begin{itemize}
  \item \textbf{Control Plane:} ...
  \item \textbf{Reader:} ...
  \item \textbf{Writer:} ...
  \item In a representative task, the system used ... and completed ... in ..., with throughput around ....
\end{itemize}
```

Keep terminology stable with the user's wording. If the user corrects the project purpose, update the resume headline and first sentence before changing the technical bullets.

## Clarification Rules

Ask the user only when the answer changes the output:

- Which modules should be separate pages?
- Which differentiating points should be included or excluded?
- Should this be learning material, interview preparation, resume wording, or all three?
- Is a metric safe to publish in resume material?

Do not ask for things the code/docs can answer cheaply. Read first.

## Final Check

Before finishing:

- The latest user request is answered, not an older request.
- CodeGraph was used first when available, or the fallback was stated.
- The user's differentiating points are preserved.
- Internal terms are translated to general engineering mechanisms.
- HTML has text, diagrams, tables, and passes parser validation.
- Resume claims do not exceed the evidence.
