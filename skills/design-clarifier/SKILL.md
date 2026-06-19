---
name: design-clarifier
description: Use when an AI agent or assistant is asked to design, modify behavior, write implementation plans, produce architecture/resume/documentation wording, or implement ambiguous product/engineering changes where user intent, terminology, scope, source-of-truth documents, constraints, or design tradeoffs may be unclear. Guides the agent to ground itself in available evidence, classify ambiguity, ask only decision-critical questions, confirm a design contract, and then write or implement without inventing unsupported details.
---

# Design Clarifier

## Goal

Use this skill to prevent confident but wrong design work. Treat ambiguity as a design input to resolve, not a gap to fill with generic patterns.

This workflow is platform-neutral. It can be used by coding agents, writing agents, planning agents, or product/design assistants.

## Core Principles

- Ground decisions in evidence before proposing structure.
- Preserve the user's conceptual model and terminology unless they ask to change it.
- Ask questions only when the answer changes the design, output, or implementation.
- Do not block on questions that can be answered by inspecting available sources.
- Make assumptions explicit when proceeding without confirmation.
- Update the working model immediately when the user corrects it.

## Workflow

### 1. Classify the Request

Classify the task before acting:

- **Clear execution**: requirements and source of truth are clear. Proceed, stating only important assumptions.
- **Discoverable ambiguity**: the answer likely exists in files, docs, code, examples, logs, or links. Inspect first.
- **Decision ambiguity**: multiple valid designs exist and the user's preference matters. Ask before proceeding.
- **Contract ambiguity**: scope, ownership, safety, persistence, compatibility, or success criteria are unclear. Ask before proceeding.

### 2. Gather Ground Truth

- Inspect the user's latest request and any referenced files, docs, code, links, examples, or prior decisions before proposing a design.
- Prefer project documents and existing code over generic knowledge.
- Extract the user's vocabulary and use it consistently unless the user asks to change terminology.
- Separate facts, assumptions, and unknowns in your own reasoning.
- If an external framework or article is referenced, map its terms onto the user's local design instead of replacing local terms blindly.

### 3. Stop Before Inventing

Ask the user before proceeding when any of these are unclear:

- The desired behavior, user journey, or acceptance criteria.
- The ownership boundary between modules, products, or layers.
- Whether a capability should be implemented, documented, or only discussed.
- The correct terminology or conceptual model.
- Whether a design should follow local docs/code or an external article/framework.
- Any safety, permission, persistence, compatibility, or migration policy.

Do not ask about details that can be discovered cheaply from available context or the workspace.

### 4. Ask Focused Questions

- Ask one to three questions at a time.
- Make each question concrete and decision-oriented.
- Include the impact of each option when useful.
- Avoid broad "what do you want?" questions.
- If the user has already given a direction, restate the assumption and ask only for the missing decision.
- Prefer questions that can be answered with a short phrase, choice, or correction.
- Do not mix a long proposal with clarifying questions; the user should not need to review a speculative design to answer.

Example:

```text
I need to confirm two design points before proceeding:
1. Does `@agent` mean an Orchestrator dispatch constraint, or should direct external Agent chat be supported?
2. Should the resume explicitly name OpenCode/Codex, or use the broader term "external CLI Agent"?
```

### 5. Confirm the Design Contract

Before writing a long answer, making code changes, finalizing wording, or executing a plan, produce a concise contract when the task has non-trivial ambiguity:

- Goal: what will be achieved.
- Scope: what is in and out.
- Source of truth: docs/code/user decisions being followed.
- Key design choices: 3-6 bullets.
- Open issues: only unresolved items that block progress.

If the user confirms or corrects it, continue from the corrected contract.

Use this compact format when helpful:

```text
Design contract:
- Goal:
- In scope:
- Out of scope:
- Source of truth:
- Key choices:
- Open blockers:
```

### 6. Execute Against the Contract

- Keep output aligned with the confirmed model.
- Use the user's preferred terms, not nearby generic alternatives.
- Do not split or merge concepts differently after confirmation unless you explain why and ask.
- For writing tasks, emphasize design reasoning and mechanism over feature lists.
- For implementation tasks, keep edits scoped to the confirmed boundaries and verify behavior.
- For writing tasks outside a code workspace, treat the user's source material, terminology, and examples as the source of truth.

### 7. Self-Check Before Finalizing

Before the final answer or patch, check:

- Did I use the same conceptual model the user confirmed?
- Did I rely on docs/code when the user asked me to?
- Did I introduce unsupported terminology or claims?
- Did I accidentally turn one concept into two, or merge concepts the user wanted separate?
- Did I answer the latest request rather than an earlier thread state?

If any answer is no, correct the work before responding.

## Response Style

- Be direct about uncertainty.
- Say "I did not find X in the referenced docs/code" when a term or behavior is not found.
- Prefer a small number of high-signal clarifying questions over a full speculative plan.
- Do not use boilerplate phrases like "best practice" unless tied to local evidence.
- If corrected by the user, update the working contract immediately and do not defend the previous wording.

## Anti-Patterns

- Inventing architecture or terminology because it is common in similar systems.
- Treating a user's correction as a wording issue when it changes the design model.
- Asking many questions before inspecting available sources.
- Producing a polished plan while key ownership or behavior remains unknown.
- Reusing an earlier answer after the user has changed the latest request.
- Hiding assumptions inside confident prose.
