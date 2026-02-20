# Interview Guide v3.1 - Interaction Flow

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

## Variant Handling
- Keep UC-001 and UC-002 in the same workflow program.
- Maintain variant scope/status in `working/<product>/interview/usecases.md`.
- Do not merge UC-001 and UC-002 rules prematurely.

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
1. Does this succinct workflow reflect your real process at high level?
2. Which step/handoff is missing or incorrect?
3. What exceptions/alternate flows happen in real cases?

### 2. Step-by-Step Evidence Capture
What I discuss:
- For each workflow step, capture exact inputs, outputs, IDs, and evidence artifacts.

What I get:
- System/data handoff map and traceability anchors.

Questions:
1. For this step, what exact inputs must exist before action?
2. What exact outputs are produced, and where are they stored?
3. Which IDs/keys connect this step to other systems (`ticket`, `NFR`, `branch`, `build`, `QA`, `PMDB`)?
4. How do the other roles (Dev, QA, TAM/CSE/customer-facing) participate in this step?
5. In your two-tier team model, what information is Core-only vs All-shared during this step?
6. What problems are caused by executing this step across multiple applications?
7. What business/technical knowledge could be captured but is currently missed, and why?

What I do during the discussion:
- Log each Q&A in `working/<product>/interview/QA_log.md`.
- Update workflow details in `working/<product>/interview/exist_workflow.md`.

Before exiting Step 2 discussion:
- Double-confirm the updated as-is workflow with the interviewee.

### 3. Candidate Problem Validation and Ranking
What I do before validation:
- Extract interview evidence and map it to candidate problems.

What I tell the interviewee:
- I will present candidate problems plus mapped evidence; you validate the mapping.

What I get:
- Confirmed problem set ranked by frequency x severity x business impact.

Questions:
1. Is the problem-evidence mapping correct?
2. Which problem should be split, merged, or reworded?
3. Give one recent concrete case for each confirmed problem.
4. Rank each confirmed problem by frequency, severity, and business impact.

### 4. Goal and Metric Validation
What I discuss:
- Evidence-supported problems -> goals -> measurable metrics.

What I get:
- Metrics with clear formula, data source, baseline, and target window.

Questions:
1. Does each selected goal directly address a confirmed problem?
2. For each metric, confirm formula and system of record.
3. What is current baseline and target timeline?
4. Which guardrails prevent local optimization?

### 5. AI-Agent Allocation and Control
What I discuss:
- Per workflow step: agent actions, approval gates, approver role, escalation triggers.

What I get:
- Decision-allocation matrix for PRD.

Questions:
1. Which actions can the agent execute autonomously?
2. Which actions require approval, and by whom?
3. What conditions force manual takeover/escalation?
4. What data is out of scope for agent processing?

### 6. Close and Next Session
What I tell the interviewee:
- I will summarize confirmed facts, open questions, and assumptions.

What I get:
- Agreement on what is closed now vs next session.

Questions:
1. Do you agree with this session summary and unresolved items?
2. What should be next session priority (`UC-001 next step` or `UC-002 deep dive`)?

## Current Progress Snapshot (already captured)
- Step 1 (Enter case) documented.
- Step 2 (Review TOP3 + linked requests) documented.
- TOP3 Mantis field dictionary with cross-system interpretation rules documented.
- UC-001/UC-002 split confirmed.

## Deferral Notes
- Deep linked-bug inclusion/exclusion logic is deferred to `UC-002` and to be revalidated in Step 5 PRD review.

## Appendix A - Warm-up Brief (Moved from Main Guide)

### Business Context
- PM handles critical TOP3 cases across pre-sales feature requests and post-sales issues.
- Work requires cross-functional orchestration across PM, Dev, QA, and customer-facing roles.
- Target outcome: faster and more reliable case progression from intake to delivery.

### System Context (As-Is, from assets + HPO succinct workflow)
- Mantis: TOP3 case intake, linked NFR/bug context, progress updates, release deliverable tracking.
- Microsoft Teams: two-tier collaboration model (`Core` and `All`) for execution and stakeholder sync.
- Jenkins: branch creation, build trigger, and build lifecycle handling.
- Outlook/email notifications: build success/failure awareness and escalation signal.
- InfoSite: built image lookup and artifact verification.
- Phabricator: QA progress tracking and execution workflow states.

### Candidate Problems (Top 4)
1. Cross-system context fragmentation causes PM overhead and context switching.
2. Traceability from intent to deliverable is fragmented across ticket, branch, build, QA, and artifact systems.
3. Knowledge from investigation/reproduction/root-cause handling is not systematically captured for reuse.
4. Manual coordination overhead is high across Core/All communication loops and execution systems.

### Selected 3 Problems To Tackle (Draft)
1. Context fragmentation across systems.
2. Traceability gap from intent to deliverable.
3. Weak knowledge capture/reuse during case cycles.

### Preset Goals (Draft)
1. Reduce PM manual effort to build and maintain case context across systems.
2. Establish reliable end-to-end traceability from ticket intent to delivered solution evidence.
3. Build reusable domain knowledge assets from each TOP3 case cycle.

### Success Metrics (Draft)
Goal 1 -> Reduce PM manual effort to build and maintain case context across systems.
- Primary metric: `Confirmed Correct Automation Actions` (#/case/week)
  - Formula: count of agent-proposed actions that are approved by PM and executed without rollback/correction.
  - Measurement source: agent action log with PM approval decision and post-action outcome.
- Supporting metric: `Approval-Gated Automation Coverage` (% eligible actions)
  - Formula: (eligible actions executed by agent only after PM confirmation / total eligible actions) * 100.
- Supporting metric: `Agent Action Correction Rate` (% executed actions)
  - Formula: (agent-executed actions that require PM correction or rollback / total agent-executed actions) * 100.

Goal 2 -> Establish reliable end-to-end traceability from ticket intent to delivered solution evidence.
- Primary metric: `Trace Completeness Rate` (% cases)
  - Formula: (cases with all required links present / total closed TOP3 cases) * 100.
  - Required links: Mantis ticket -> branch/build -> QA evidence -> artifact link.
- Supporting metric: `Trace Defect Rate` (% cases)
  - Formula: (cases with missing or incorrect trace link / total reviewed cases) * 100.

Goal 3 -> Build reusable domain knowledge assets from each TOP3 case cycle.
- Primary metric: `Knowledge Capture Completeness` (% closed cases)
  - Formula: (closed cases with all required knowledge fields captured / total closed cases) * 100.
  - Required fields: symptom, repro steps, root cause, fix/workaround, validation evidence.
- Supporting metric: `Knowledge Reuse Rate` (% new cases)
  - Formula: (new cases that reused at least one prior captured knowledge asset / total new cases) * 100.

Cross-goal outcome metric (North-star):
- `P50 Assignment-to-Verified-Delivery Time` (hours)
  - Formula: median time from TOP3 assignment timestamp to verified delivery package completion timestamp.
