# Interrogation Guidance

## Objective

- Remove assumptions before drafting or refining PRD content.
- Collect evidence that can be traced to workflow, systems, roles, constraints, and measurable outcomes.
- Make sure any reader can execute the as-is workflow without asking the original interviewee for clarification.

## Step Boundary Rules

- Explicitly announce when entering a workflow step and when exiting it.
- During `Step 0 Initialization`, do not ask decision-allocation questions (autonomous vs approval-required actions).
- Ask decision-allocation questions in `Step 2 User Interview` and finalize them in `Step 5 Review and Refine`.
- Log progress in `working/<product>/progress.md` after completing each workflow step.

## Questioning Protocol

- Ask questions one by one unless the human product owner asks for grouped questions.
- Keep each question concrete and scoped to one missing decision or one missing fact.
- If the answer is partial, immediately ask only the missing fields.
- If the answer is unclear, restate current understanding and ask for correction.
- Use examples when the counterpart asks for definition clarification.

## Minimum Discovery Coverage Before Drafting PRD

- Business outcome and failure conditions.
- Primary users and role boundaries.
- As-is workflow with triggers, actions, systems, outputs, and decisions.
- Existing systems and artifacts, including access constraints.
- Problems ranked by frequency, severity, and impact.
- Data sources in-scope and off-limits.
- North-star metric and guardrail metrics with targets and time window.
- AI-agent action allocation:
  - Autonomous actions
  - Approval-required actions
  - Approver roles

## Interview Tactics That Worked

- Enumerate artifacts one by one when reviewing an asset folder.
- For each non-supporting artifact, request:
  - Source system
  - Artifact type
  - PM usage in workflow
  - Access/sensitivity
- Treat `*_files` folders as supporting assets by default to reduce repetitive questions.
- Ask for explicit acceptance when proposing metric targets.

## Anti-Patterns To Avoid

- Jumping to later-step questions during Initialization.
- Advancing steps without logging completion in `progress.md`.
- Asking broad multi-topic questions that mix workflow, metrics, and governance.
- Assuming implied constraints, data permissions, or automation scope.

## Completion Criteria For Interrogation

- No unresolved assumptions remain for the current step.
- Every open item is explicitly listed with owner and follow-up step.
- Outputs required by the current step are saved in the correct workspace paths.
