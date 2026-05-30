# QA

## Objective
- To verify that the implementation of subagent delegation rules across `AGENTS.md` and the three skill files (`research-me`, `prototype-me`, `execute-me`) accurately reflects the PRD requirements and does not break the 7-phase workflow invariants.

## Scope under test
- Features or flows under review: Subagent Delegation rules and prompt assembly guidelines.
- Completed tickets included: ISSUE-001, ISSUE-002, ISSUE-003, ISSUE-004.
- Explicitly out of scope for this pass: Execution of an actual subagent (this QA pass verifies the *rules* and *markdown structures*, not runtime extension behavior), and `.mjs` validation scripts (which were out of scope for changes).

## Source artifacts
- `docs/prd.md`
- `docs/issues.md`
- `AGENTS.md`
- `.pi/skills/research-me/SKILL.md`
- `.pi/skills/prototype-me/SKILL.md`
- `.pi/skills/execute-me/SKILL.md`

## Build / revision under review
- Branch / commit / working tree context: Local filesystem (working tree after ISSUE-004 execution).
- Environment notes: Markdown file structural review.

## Test scenarios
1. **Orchestrator Pattern Check**: Read `AGENTS.md` to ensure it explicitly defines the Primary Agent as the Orchestrator and restricts Subagents from modifying `docs/`.
2. **Context Isolation Check**: Read `.pi/skills/execute-me/SKILL.md` to ensure it explicitly mandates isolated prompt assembly and strictly forbids `inherit_context: true`.
3. **Exploration Delegation Check**: Read `.pi/skills/research-me/SKILL.md` and `prototype-me/SKILL.md` to verify they instruct the use of `subagent_type: explore` (or general-purpose for prototypes) and correctly guide the use of `get_subagent_result`.

## Edge cases to verify
- Invalid input: Are the delegation instructions placed outside the main workflow (e.g., as an "Optional" clause) so they don't confuse an agent doing normal non-delegated work?
- Permissions / access: Does `AGENTS.md` forbid subagents from writing to `docs/prd.md` and `docs/issues.md`?

## Human review checklist
- [x] UX and behavior consistency: Do the Markdown additions look native to the existing files?
- [x] Copy / labeling sanity: Are the subagent types (`explore`, `execute`) spelled correctly?
- [x] Maintainability / readability spot check: Are the new instructions concise enough not to overwhelm the agent's context window?

## Known risks
- Agents might still hallucinate if the isolated prompt is poorly constructed by the Primary Agent, but the rules are as strict as possible within Markdown bounds.

## Findings
- **Pass:** 
  - `AGENTS.md` successfully includes the "Subagent Delegation (Orchestrator vs Worker)" section. It explicitly forbids subagents from modifying workflow state artifacts.
  - `research-me/SKILL.md` includes the "Optional: Subagent Delegation" section correctly recommending `subagent_type: explore` and text-only returns.
  - `prototype-me/SKILL.md` includes the delegation section correctly, restricting writes to `docs/prototype/` only.
  - `execute-me/SKILL.md` includes the delegation section correctly, strongly mandating `inherit_context: false` and isolated prompt assembly containing goals, scope, files, and TDD instructions.
- **Fail:** None observed.
- **Uncertain:** None.

## Follow-up issues
- New AFK tickets: None.
- New HITL tickets: None.
- Existing tickets to reopen or unblock: None.

## Release / sign-off recommendation
- Ready for release: **Yes**.
- Ready for next execution pass: N/A.
- Blocked pending HITL: No.
- Needs more testing: No. All requirements from the PRD have been met and structurally verified in the markdown files.

## Next step
- Recommended action: Use the `finish-me` skill (or consider the workflow complete).
- Why: All planning, execution, and QA passes have been successfully completed. There are no remaining risks or failed tests blocking the conclusion of this sprint.