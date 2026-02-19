# Interview Guide v2 - Interaction Flow

Date: 2026-02-18
Product: agent-top3pm
Stage: Step 2 (User Interview)
Current Focus: UC-001 (TOP3 New Feature Request)
Next Focus: UC-002 (TOP3 Bug Fixing)

## Purpose of This Interview
- Validate/correct the prepared baseline from Step 1:
  - succinct workflow,
  - candidate problems,
  - preset goals,
  - draft success metrics.
- Capture evidence-level workflow details that can be translated to PRD requirements.

## Conversation Flow

### 0. Opening and Scope
What I tell the interviewee:
- We have a prepared baseline from document review and HPO alignment.
- This session validates reality, corrects assumptions, and captures evidence.

What I get:
- Interviewee identity (name/title/role).
- Session scope and use-case variant (`UC-001` now, `UC-002` later).

Questions:
1. Please confirm your name and current title/role.
2. Today we focus on which variant: `UC-001 NFR` or `UC-002 Bug Fixing`?
3. Any scope boundaries for this session?

### 1. Baseline Workflow Playback and Correction
What I tell the interviewee:
- I will replay the succinct workflow baseline.
- Please correct missing steps/handoffs/decision points/exceptions.

What I get:
- Validated as-is workflow with trigger, actor, tool, artifact, and decisions per step.

Questions:
4. Does this succinct workflow reflect your real process at high level?
5. Which step/handoff is missing or incorrect?
6. What exceptions/alternate flows happen in real cases?


### 2. Step-by-Step Evidence Capture
What I discuss:
- For each workflow step, capture exact inputs, outputs, IDs, and evidence artifacts.

What I get:
- System/data handoff map and traceability anchors.

Questions:
7. For this step, what exact inputs must exist before action?
8. What exact outputs are produced, and where are they stored?
9. Which IDs/keys connect this step to other systems (`ticket`, `NFR`, `branch`, `build`, `QA`, `PMDB`)?

--------HPO updated--------

10. How the other roles--Dev, QA, TAM/CSE(sales/customer-facing)--participate in during this step?
11. During this step, in your two-tier team model, what information is Core-only vs All-shared?
12. What problems are caused by the situation that the workflow is executed across many applications?
13. What business/technical knowledge could be but not have been captured, and why?

----------------------------

What I do during the discussion:
- log each Q&A in ***working/<product>/interview/QA_log***.
- update the succinct workflow, using the information gathered to enrich the workflow and update ***working/<product>/interview/exist_workflow_<interviewee>.md***. 

before exiting the step 2 discussion:
- double confirm the existing workflow with the interviewee.


### 3. Candidate Problem Validation and Ranking


What I do before the validation:
- Extract the evidences stated by the interviewee and map them to the candidate problems 

What I tell the interviewee:
- I will present prepared candidate problems and the mapped evidences; you validate the map.


What I get:
- Confirmed problem set ranked by frequency × severity × business impact.

Questions:
14. Is the problem-evidence map correct?
15. Which problem should be split/merged/reworded?
16. Give one recent concrete case for each confirmed problem.
15. Rank each by frequency, severity, business impact.

### 4. Goal and Metric Validation
What I discuss:
- evidence supported problems -> goals -> measurable metrics.

What I get:
- Metrics with clear formula, data source, baseline, and target window.

Questions:
16. Does each selected goal directly address a confirmed problem?
17. For each metric, confirm formula and system of record.
18. What is current baseline and target timeline?
19. Which guardrails prevent local optimization?

### 5. AI-Agent Allocation and Control
What I discuss:
- Per workflow step: agent actions, approval gates, approver role, escalation triggers.

What I get:
- Decision-allocation matrix for PRD.

Questions:
20. Which actions can agent execute autonomously?
21. Which actions require approval, and by whom?
22. What conditions force manual takeover/escalation?
23. What data is out of scope for agent processing?

### 6. Close and Next Session
What I tell the interviewee:
- I will summarize confirmed facts, open questions, and assumptions.

What I get:
- Agreement on what is closed now vs next session.

Questions:
24. Do you agree with this session summary and unresolved items?
25. What should be next session priority (`UC-001 next step` or `UC-002 deep dive`)?

## Current Progress Snapshot (already captured)
- Step 1 (Enter case) documented.
- Step 2 (Review TOP3 + linked requests) documented.
- TOP3 Mantis field dictionary with cross-system interpretation rules documented.
- UC-001/UC-002 split confirmed.

## Deferral Notes
- Deep linked-bug inclusion/exclusion logic is deferred to `UC-002` and to be revalidated in Step 5 PRD review.
