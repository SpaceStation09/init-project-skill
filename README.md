# Init Project Skill

`init-project-skill` is a platform-independent, specification-driven workflow for starting or initializing software projects. It helps turn an early project idea into a reviewed product definition, a technical design, and practical instructions for future agents.

The skill is designed for project initialization—not application implementation. It focuses on asking the right questions, recording decisions, and keeping product requirements separate from technical choices.

## What the Project Provides

The workflow guides a project through four phases:

1. **Workspace preparation** — Inspect the repository, existing documentation, and project conventions.
2. **Product requirements** — Define the problem, users, scope, user journeys, functional requirements, acceptance criteria, and success metrics in a PRD.
3. **Technical co-design** — Select and document the architecture, technology stack, data model, APIs, directory structure, testing strategy, and deployment approach in a SPEC.
4. **Agent instructions** — Generate an `AGENTS.md` file that gives future agents concise, project-specific operating guidance.

When the workflow is completed, the intended project artifacts are:

```text
specs/PRD.md   # Product requirements and acceptance criteria
specs/SPEC.md  # Confirmed technical design
AGENTS.md      # Operational instructions for future agents
```

These artifacts are created or updated as the user confirms each phase. They are expected outputs of the workflow, not files currently included in this repository.

## Repository Structure

The current repository is intentionally minimal:

```text
.
├── README.md   # Project overview and usage guide
└── SKILL.md    # Skill metadata, workflow rules, and artifact requirements
```

`SKILL.md` is the source of truth for the workflow. It defines the skill's trigger conditions, interview protocol, confirmation gates, required document sections, and rules for handling assumptions and unresolved decisions.

## Key Characteristics

- **Specification first:** Product intent is documented before technical design.
- **User-confirmed decisions:** The workflow does not silently choose product scope or major technical decisions on the user's behalf.
- **Traceable artifacts:** Requirements, design choices, and agent instructions are kept in separate documents with explicit links between them.
- **Safe initialization:** Existing `PRD.md`, `SPEC.md`, or `AGENTS.md` files are not overwritten without first explaining the proposed change and getting permission.
- **Minimal assumptions:** Unknown information is marked as `TBD` or as an explicit assumption instead of being presented as fact.
- **No automatic implementation:** The skill does not write application code, install dependencies, initialize repositories, or deploy projects unless the user explicitly requests a separate follow-up.

## Typical Use

Use this skill when starting a new project or formalizing an existing idea. It is intended for requests such as:

- creating or initializing a project;
- defining a PRD, SPEC, or software design workflow;
- turning an idea into an approved product and technical specification;
- preparing project-specific `AGENTS.md` instructions.

The workflow asks questions in stages, uses information already present in the workspace where possible, and pauses for explicit confirmation before moving from product requirements to technical design and from technical design to agent instructions.

## Document Responsibilities

| Artifact | Responsibility |
| --- | --- |
| `specs/PRD.md` | Defines what to build, why it matters, who it serves, and how success is evaluated. |
| `specs/SPEC.md` | Defines how the confirmed product should be implemented, including architecture, technology choices, data, APIs, and testing. |
| `AGENTS.md` | Gives future coding agents practical rules for working in the project without duplicating the full PRD or SPEC. |

## Scope and Limitations

This repository contains workflow instructions rather than an executable application. It has no runtime, package manager, external service configuration, or application test suite. Commands, technologies, and project-specific conventions should be added only after they are confirmed for the project being initialized.

## License

No license has been specified for this repository yet.
