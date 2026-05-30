# Absorb

## Objective
To build a trustworthy pre-ideation context and understand the baseline of the `pi-7-phase-subagents` repository, enabling an AI agent to effectively assist in maintaining or extending this template project using the 7-phase workflow.

## Repo snapshot
- **Stack**: Markdown (artifacts & documentation), JavaScript (local validation scripts), Pi Agent framework configuration (`.pi/skills`).
- **Purpose**: A master template repository providing a structured, "skill-first" operating model for Pi-based AI agents, implementing a 7-phase AI-assisted development workflow.

## Product/domain understanding
This project defines the guardrails and operational flows for an AI coding agent. It emphasizes explicit phase gates—preventing the agent from prematurely jumping into implementation before problem definition (Idea), requirements (PRD), and planning (Issues) are complete and validated. It uses local markdown files in `docs/` as the state-holding cross-session source of truth.

## Architecture map
- `.pi/skills/`: Executable workflow layer containing 11 specific skills (e.g., `absorb-me`, `grill-me`, `prd-me`).
- `docs/`: Cross-session source of truth artifacts (`absorb.md`, `idea.md`, `prd.md`, `issues.md`, `qa.md`, etc.).
- `scripts/`: Local validation scripts (`validate-readiness-gates.mjs`, `validate-planning-closeout-guidance.mjs`) to enforce workflow state and ensure artifact compliance.
- `AGENTS.md`: Canonical rules and invariants for the AI agents operating within this project.
- `MASTER_TEMPLATE.md`: Instructions for maintaining the template itself, ensuring clean state.

## Critical flows relevant to upcoming change
- **Absorb Gate**: The `absorb-me` skill must be run to generate this document before any new feature ideation (`grill-me`) begins in an existing project.
- **Strict Phase Transitions**:
  - `Idea` -> `PRD`: Requires explicit `Ready for next phase: yes` and absorb gate decision.
  - `PRD` -> `Issues`: Requires explicit `Planning approval: approved for issues planning (correctness and scope)`.
- **Execution Flow**: `execute-me` claims exactly one `AFK` ticket from `docs/issues.md` per run, defaulting to TDD, and requiring fresh validation evidence to mark as done.

## Constraints and contracts
- Agents must follow the skill-first paradigm and strict progression logic in `AGENTS.md`.
- `docs/absorb.md` must be kept fresh (baseline: 14 days, goes stale instantly on major architecture or flow changes).
- The `finish-me` skill is restricted to making lightweight closeout recommendations, not performing git automation or releasing.
- Only one prototype winner can feed into a PRD.
- Template cleanliness must be maintained (no lingering temporary code or specific project data in the master template artifacts).

## Validation entry points (tests/commands)
- Node.js validation scripts:
  - `node scripts/validate-readiness-gates.mjs`
  - `node scripts/validate-planning-closeout-guidance.mjs`
- CI enforces these script checks to prevent invalid state transitions.

## Risks/hotspots
- **Workflow Circumvention**: The main risk is the AI agent ignoring the strict invariants in `AGENTS.md` (e.g., jumping straight to code, taking multiple tickets).
- **Template Drift**: Accidentally committing project-specific work into the empty template `docs/` files.
- **Closeout Scope Creep**: The `finish-me` helper expanding beyond recommendations into unauthorized git operations or overly heavy checks.

## Unknowns & assumptions
- Assumption: The user intends to use this repository as the master template and may want to either improve the template structure, update the validation scripts, or add a new skill.
- Unknown: What specific new feature or change the user intends to implement next in this repository.

## Questions to seed grill-me
1. Are we planning to introduce a new skill, update an existing workflow phase, or refine the template's internal validations?
2. If we are modifying a workflow phase, how do we ensure it doesn't break the strict readiness gates defined in `AGENTS.md` and `scripts/validate-readiness-gates.mjs`?
3. Which current behavior and flow (e.g., execution invariants, absorb gate logic) must be preserved in the upcoming change?

## Freshness note
- **Refresh date**: 2026-05-30
- **Scope**: Complete repository architecture, workflow definitions, and validation scripts.
- **Rule**: Baseline valid for 14 days unless stale trigger(s) fire sooner.
- **Stale triggers active**: None.

## Recommended next step
`grill-me`