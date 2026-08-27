---
name: init-project-skill
description: Use when starting or initializing a software project and the user wants a specification-driven workflow that interviews them to produce and approve a PRD, collaboratively selects the technical stack to produce and approve a SPEC, and then writes a project-specific AGENTS.md; trigger on 创建项目、启动项目、项目初始化、PRD、SPEC、SDD、规范驱动开发.
---

# SDD Project Initialization

## Mission

Help the user turn an initial software idea into three reviewed project artifacts:

1. `specs/PRD.md`: what to build and why.
2. `specs/SPEC.md`: the user-confirmed technical design and how to build it.
3. `AGENTS.md`: practical instructions for future agents working in the project.

This skill is a planning and documentation workflow. Do not implement application code, install project dependencies, initialize a repository, or deploy anything unless the user explicitly asks for a separate follow-up.

## Operating Rules

- Treat the current working directory as the project root unless the user explicitly names another directory.
- Inspect the workspace and existing documentation before asking questions that the workspace can answer.
- Never overwrite an existing `PRD.md`, `SPEC.md`, or `AGENTS.md` without explaining what will change and getting permission.
- Preserve existing project conventions when they are clear. Use `specs/` for new specification files unless the user chooses another location.
- Do not invent requirements, technical constraints, commands, or project facts. Mark unknowns as `TBD` or as explicit assumptions.
- Keep product decisions separate from technical decisions. A technical proposal must not silently change the approved PRD.
- Treat direct user instructions as authoritative. If a user choice conflicts with the PRD or introduces a material risk, explain the conflict and let the user decide whether to change the choice or accept the risk.
- Follow the user's language for questions and documents unless they request another language.

## Interview Dependency

At the beginning, try to load the `grilling` skill through the host skill mechanism. Use `grilling` as the interview engine when it is available. Do not use `grill-with-docs` as a substitute for this workflow: it has different stateful outputs (`CONTEXT.md` and ADRs), and those outputs are not a complete PRD decision record.

If `grilling` is not available, tell the user plainly and offer these choices:

1. Install or enable `grilling`, then retry this skill.
2. Continue without it using this skill's built-in interview fallback.
3. Stop the project initialization.

Do not install external skills automatically without the user's consent. If the user chooses installation, wait for the host to confirm that the skill is available before relying on it. If the host requires a restart or a new invocation for skills to load, tell the user that and stop the current workflow.

If the user chooses the fallback, conduct the interview yourself using the rules in `## Interview Protocol`. The fallback is not permission to answer the user's product or technical decisions on their behalf.

The interview should run in the host's normal execution mode, not Plan Mode. If the current mode would cause the agent to rush toward a plan instead of asking questions, ask the user to switch modes before continuing.

## Interview Protocol

Whether it is provided by `grilling` or by the fallback, the interview must follow these rules:

- Model the project as a decision tree and ask only the current frontier: questions whose prerequisites are already settled.
- Ask questions in answerable rounds rather than dumping a long questionnaire. Ask one question at a time if the user requests that rhythm.
- Number each question, give it a short title, explain why it matters, and provide a clearly labelled recommendation when a recommendation is possible.
- Separate facts from decisions. Read files or research facts with tools; wait for the user's answer on preferences, scope, priorities, and trade-offs.
- Do not answer an open decision yourself merely to make progress.
- Accept `I don't know`, `TBD`, and deliberate deferral as valid answers. Explain what consequence the uncertainty has.
- Recompute the next questions after each round. Reopen a dependent decision if a later answer invalidates an earlier assumption.
- Keep a running record of all important answers. Do not rely on `CONTEXT.md`, ADRs, or the conversation alone to preserve decisions.
- End each phase with a concise understanding summary and an explicit confirmation request.

When the workspace contains code or documents, questions that can be answered from that material are facts, not user decisions. In a greenfield directory, ask the user for decisions instead of pretending that defaults are project facts.

## Phase 0: Prepare the Workspace

1. Inspect the current directory, its existing specification files, agent instructions, and relevant project documents.
2. Establish whether this is a new project, an existing project being documented, or an existing specification being continued.
3. If an artifact already exists, offer to continue it, update it in place, or create a separately named version. Do not overwrite it implicitly.
4. Confirm that the user wants to begin the SDD workflow and identify the project name if it is not obvious.

After the first substantive answers, create `specs/` if needed and maintain `specs/PRD.md` as a clearly marked `Draft`. Update the draft as the interview resolves important decisions so that the work can be resumed if the session ends.

## Phase 1: Product Requirements Interview

Use `grilling` if available. Scope the interview around the product, not the implementation. Cover the applicable parts of these areas:

- Problem, opportunity, and intended outcome.
- Target users, roles, permissions, and affected stakeholders.
- Primary user journeys and important edge cases.
- MVP scope and explicit non-goals.
- Functional requirements and business rules.
- User stories and Given/When/Then acceptance criteria.
- Non-functional requirements such as performance, security, privacy, accessibility, compatibility, and availability.
- External integrations, regulatory or operational constraints, and success metrics.
- Assumptions, risks, unresolved questions, and deliberate deferrals.

If the user mentions a technology during this phase, record it as a user-stated constraint or preference. Do not treat it as a completed technical design until it has been examined during Phase 2.

### PRD Structure

`specs/PRD.md` must contain, as applicable:

```markdown
# Product Requirements Document

- Status: Draft | Approved
- Version:
- Last updated:

## 1. Overview
## 2. Problem and goals
## 3. Users and roles
## 4. Scope and non-goals
## 5. User journeys
## 6. Functional requirements
## 7. Acceptance criteria
## 8. Business rules and domain terms
## 9. Non-functional requirements
## 10. Success metrics
## 11. Assumptions, risks, and open questions
## 12. Change log
```

Give functional requirements stable IDs such as `FR-001` and non-functional requirements IDs such as `NFR-001`. Every MVP requirement must have an acceptance criterion. Keep confirmed facts, user decisions, assumptions, and `TBD` items distinguishable.

### PRD Confirmation Gate

Before Phase 2:

1. Summarize the product, MVP boundary, non-goals, and unresolved items.
2. Point out contradictions, missing acceptance criteria, and assumptions.
3. Ask the user to confirm the PRD or request revisions.
4. Do not start technical design until the user explicitly confirms the PRD.

## Phase 2: Technical Co-Design

Read the confirmed PRD and extract the technical decisions needed to implement it. The SPEC is co-designed with the user; it is not an automatic translation of the PRD.

Use `grilling` for the technical decision interview when available. Otherwise use the same built-in fallback protocol. Ask only decisions that affect this project. Typical decision areas include architecture style, language and runtime, frontend and backend frameworks, database and data access, API style, authentication and authorization, file or object storage, messaging, external services, deployment target, testing, observability, and package or workspace structure.

### When the User Has a Technical Preference

If the user already names a language, framework, database, hosting platform, or other technology:

1. Ask what motivated the choice and which constraint or priority it serves.
2. Check the choice against the PRD's functional and non-functional requirements.
3. Examine compatibility, maturity, operational complexity, cost, security, performance, maintainability, and the user's stated experience where relevant.
4. Explain material risks or mismatches and offer alternatives when useful.
5. Ask the user to confirm the choice after the trade-offs are clear.

Do not reject a user choice merely because another option is more common. Make the reasoning and consequences explicit, then preserve the user's confirmed decision.

### When the User Has No Technical Preference

For each meaningful decision, present a small set of viable options, normally two or three. For every option include:

- Fit with the PRD.
- Main advantages.
- Main disadvantages and operational cost.
- Important risks or constraints.
- A recommendation and why it is recommended.

Wait for the user to choose or adjust the proposal. The recommendation is input to the decision, not the decision itself.

Low-risk, reversible implementation details may be selected by the agent after the user confirms the governing principles. Record those details as agent-decided implementation details and do not present them as user choices.

If a technical choice exposes a product trade-off, pause technical design, explain the impact, revise the relevant PRD section with the user, and require PRD confirmation again before continuing.

## Phase 3: Write the Technical Specification

Create or update `specs/SPEC.md` only after the technical decisions are sufficiently settled. Keep its status as `Draft` until the user confirms it.

The following five sections are mandatory because they define the minimum technical specification expected by this workflow:

### 1. System Architecture

Describe the overall architecture, system boundaries, major components, component responsibilities, communication paths, important data flows, and interactions with external systems. Use a diagram when it makes the boundaries or flow clearer.

### 2. Technology Selection

List the selected technologies for the applicable layers, including language, runtime, frontend, backend, database, data access, authentication, storage, infrastructure, testing, and deployment. For each decision record the rationale, considered alternatives, trade-offs, risks, and confirmation status.

### 3. Data Model

Describe entities or tables, fields, types, requiredness, defaults, constraints, indexes, relationships, lifecycle rules, migrations, and seed data where applicable. Keep the model consistent with the API and business rules in the PRD.

### 4. API Interfaces

For every applicable API describe the method, URL, purpose, authentication and authorization, request parameters or body, response structure, status codes, error format, validation, pagination, versioning, idempotency, and rate limits. Mark non-applicable items explicitly rather than inventing endpoints.

### 5. Project Directory Structure

Provide a proposed directory tree and explain the responsibility of each important directory or module. Describe dependency direction and module boundaries. Do not claim that files exist if this skill has not created them.

The SPEC should also cover these supporting sections when they apply:

```markdown
# Technical Specification

- Status: Draft | Approved
- Version:
- Source PRD: specs/PRD.md
- Last updated:

## 1. System architecture
## 2. Technology selection
## 3. Data model
## 4. API interfaces
## 5. Project directory structure
## 6. Core workflows and state transitions
## 7. Security and permissions
## 8. Non-functional requirement implementation
## 9. Testing strategy
## 10. Build, run, and deployment approach
## 11. PRD-to-SPEC traceability
## 12. Risks, assumptions, and open decisions
## 13. Decision log
```

For each PRD requirement, provide a trace to the relevant architecture component, data model, API, workflow, or test strategy. Do not mark a SPEC complete while a required section contains an unlabelled guess.

### SPEC Confirmation Gate

Before generating `AGENTS.md`:

1. Summarize the confirmed technology stack, architecture, key trade-offs, and remaining `TBD` items.
2. Check that the five mandatory sections are complete and mutually consistent.
3. Check that the SPEC does not add unapproved product scope.
4. Ask the user to confirm the SPEC or request revisions.
5. Do not generate the final `AGENTS.md` until the user explicitly confirms the SPEC.

## Phase 4: Generate AGENTS.md

After SPEC confirmation, create or update `AGENTS.md` at the project root. If it already exists, inspect it and ask whether to merge, replace specific sections, or preserve it before editing.

`AGENTS.md` is an operational guide for future agents, not a copy of the PRD or SPEC. It must be derived only from confirmed project information and must link to the detailed documents. Include the applicable sections below:

```markdown
# Project Agent Instructions

## Project overview
## Source of truth
## Confirmed technology stack
## Architecture and module boundaries
## Repository map
## Development commands
## Testing and quality gates
## Coding and naming conventions
## Security and data handling
## Change workflow
## Prohibited actions
## Known TBD items
```

The generated instructions should tell future agents to read `specs/PRD.md` for product intent and `specs/SPEC.md` for technical decisions before changing the project. Include only commands that are confirmed by the SPEC or already present in the workspace. Use `TBD` for commands or conventions that are not established yet.

At minimum, future agents should be instructed to:

- Preserve the PRD's scope and acceptance criteria.
- Follow the confirmed SPEC rather than introducing an unreviewed stack or architecture.
- Avoid adding dependencies or changing public interfaces without checking the SPEC and recording the decision.
- Run the applicable tests and quality checks before declaring work complete.
- Protect secrets and avoid exposing private data in source, logs, tests, or documentation.
- Surface conflicts between user instructions, AGENTS.md, PRD, and SPEC instead of silently choosing one.

## Resuming an Interrupted Workflow

When this skill is invoked in a directory that already contains these artifacts:

- A `Draft` PRD means resume or revise the product interview.
- An `Approved` PRD without an approved SPEC means resume technical co-design.
- A `Draft` SPEC means resume technical review and confirmation.
- An approved SPEC without `AGENTS.md` means generate the agent instructions.
- Existing approved artifacts are inputs, not permission to overwrite them.

Read the change logs and open questions before asking a new round. Do not repeat settled questions unless new information creates a contradiction.

## Completion Report

When the workflow completes, report:

- The paths and status of `PRD.md`, `SPEC.md`, and `AGENTS.md`.
- The technology and architecture decisions confirmed by the user.
- Remaining assumptions, risks, and `TBD` items.
- Any files that were preserved or intentionally not changed.
- That no application code, dependency installation, or deployment was performed by this skill.
