# Interview Guidance

## Objective

- Collect high-signal evidence about current workflow, systems, pain points, and AI-agent fit.
- Remove assumptions before drafting or refining PRD content.
- Build traceable inputs for Step 2 outputs in `working/<product>/interview/`.

## Step Rules

- Announce step entry and step exit explicitly.
- Ask questions one by one by default.
- If a response is partial, ask only for missing fields.
- During interview, include decision-allocation questions:
  - What the agent can do autonomously
  - What requires approval
  - Who approves

## Required Coverage

- Business outcomes and failure conditions.
- User roles and boundaries.
- As-is workflow (trigger, actions, tools, outputs, decisions).
- Existing systems and artifact inventory.
- Pain points (frequency, severity, business impact).
- Data sources in-scope and off-limits.
- Constraints (security, compliance, budget, timeline, integrations).
- Metrics (north-star + guardrails, targets, time window).
- AI-agent action allocation and escalation rules.

## Interview Sequence (Default)

1. Product outcome and scope boundary.
2. Primary users and role responsibilities.
3. As-is workflow for one target role (end to end).
4. System/tool inventory and purpose per workflow step.
5. Pain points and impact ranking.
6. Constraints and non-negotiables.
7. Data source allow-list and off-limits list.
8. Failure conditions.
9. Success metrics and targets.
10. Agent action allocation and approval model.

## Question Format

- Use concrete prompts with explicit output shape.
- Example:
  - "List steps in order. For each step include: trigger, action, tool, output, decision point."
  - "For each pain point include: statement, frequency, severity, business impact, example."
  - "For each tool include: source system, artifact type, PM usage, access/sensitivity."

## Artifact-Driven Interviewing

- If an asset folder is provided, enumerate artifacts one by one.
- For `*_files` folders, treat as supporting assets automatically unless user says otherwise.
- Allow "skip for now" and keep a skip list for follow-up.

## Output Mapping (Step 2)

- Q&A transcript -> `working/<product>/interview/QA_log.md`
- Existing applications and purpose -> `working/<product>/interview/exist_apps.md`
- As-is workflow -> `working/<product>/interview/exist_workflow.md`
- Use cases -> `working/<product>/interview/usecases.md`
- Problem ranking -> `working/<product>/interview/problems.md`
- Interview summary -> `working/<product>/interview/interview_summary_<username>.md`

## Completion Criteria

- No unresolved assumptions for current role scope.
- Open items and skipped artifacts are explicitly listed.
- All required outputs are drafted or have a clear next action owner.
