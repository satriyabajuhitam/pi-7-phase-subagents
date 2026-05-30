# Idea

## Problem statement
The current 7-phase workflow template operates linearly and relies solely on the primary agent. There is an opportunity to leverage a newly created subagent extension to handle parallel tasks or complex delegations (e.g., research, prototyping, or bounded execution) without breaking the strict workflow gates.

## Desired outcome
To integrate the subagent extension into the project-local workflow skills (specifically targeting `research-me`, `prototype-me`, and potentially `execute-me`) to improve efficiency and capability, while strictly adhering to the existing 7-phase constraints and repository boundaries.

## Scope
- Updating local `.pi/skills/` (`research-me`, `prototype-me`, `execute-me`) to utilize subagents.
- Updating `AGENTS.md` to establish Orchestrator vs. Worker rules.
- Ensuring the subagent usage respects the "one AFK ticket per run" rule.
- Bounded to this specific repository (no global extension modifications).

## Non-goals
- Modifying the global subagent extension codebase.
- Changing the core philosophy of the 7-phase workflow (e.g., we will not skip PRD or Planning phases).
- Modifying local CI validation scripts (`scripts/*.mjs`) to track subagent usage.

## Constraints
- Changes must be confined to the project repository.
- Workflow invariants from `AGENTS.md` (e.g., strict phase gates, 1 ticket/run) cannot be violated by the subagent.
- The Primary Agent acts as the Orchestrator and Gatekeeper; subagents are strictly workers/delegates.
- Prompt isolation for subagents (no uncurated `inherit_context`).

## Assumptions
- The subagent extension is already installed and accessible via standard tool calls within this environment.

## Decision map
- **Goals & Constraints:** Local scope only, integrate subagents into workflow, strict Orchestrator pattern.
- **Design/Architecture:** 
  - Subagents return text results; Primary Agent updates `docs/`.
  - Context Management: Isolated/curated prompts for subagents.
- **Dependencies:** Subagent tool availability.
- **Risks:** Subagents bypassing readiness gates or drifting scope.

## Questions asked
1. What specific role will the subagent extension take over or assist with? (Answered: exploration and execution delegation).
2. How will we manage artifact state changes? (Answered: Primary Agent orchestrates and writes to `docs/`).
3. What are the tool authorization boundaries for subagents per phase? (Answered: role-based `explore` and `execute` subagents).
4. How is task context passed to the subagent? (Answered: Explicit, curated prompt assembly, explicitly avoiding `inherit_context: true`).
5. Do we need to update CI validation scripts? (Answered: No, enforcing it via `AGENTS.md` and `.pi/skills/*` instructions is sufficient).

## Decisions made
1. Subagents will be integrated primarily into exploratory phases (`research-me`, `prototype-me`) and cautiously into `execute-me`.
2. **Orchestrator Pattern**: Subagents are forbidden from modifying core state artifacts (`docs/`). They report findings back to the Primary Agent.
3. **Authorization Boundaries**: `research-me` delegates to `explore`, `execute-me` delegates to `execute`.
4. **Context Management**: The Primary Agent must construct a sterile, highly-focused prompt when delegating. `inherit_context: false` will be the standard.
5. **No Script Changes**: We will rely on prompt engineering and invariant rules in `AGENTS.md` to control subagent behavior, rather than modifying the `.mjs` validation scripts.

## Open questions
- None remaining. All major architectural and boundary decisions are resolved.

## Need research?
- No

## Need prototype?
- No

## Biggest risk
- The instructions in the skill files might become too complex, causing the Primary Agent to get confused between doing the work itself vs delegating it. Clear structural separation in the `SKILL.md` files is required.

## Recommended next step
- Proceed to Phase 4 (PRD) handoff.

## Absorb gate decision
- Absorb required: yes
- Skip reason: N/A
- Scope justification: Repo workflow modification
- Compensating action: N/A
- HITL approval required: no
- Approved by: N/A
- Approval timestamp: N/A

## Handoff to PRD
- **Handoff Checklist:**
  - [x] Scope is well defined and bounded.
  - [x] Role of Subagent vs Primary Agent clarified.
  - [x] Constraints are documented.
  - [x] Target artifacts identified (`AGENTS.md`, `.pi/skills/research-me/SKILL.md`, `.pi/skills/prototype-me/SKILL.md`, `.pi/skills/execute-me/SKILL.md`).
- Request is narrow enough for one coherent PRD.
- Ready for next phase: yes
- Primary blocker: None
