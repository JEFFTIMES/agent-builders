# Interview Guide - agent-top3pm

Date: 2026-02-15
Step: 1 Output (Warm-up brief + interview questions)

## Warm-up Brief

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

## Interview Questions (Step 2)

### Opening Protocol (Run Before Section A)
1. Please confirm your name and current title/role.
2. I will summarize the current succinct workflow baseline from preparation; please confirm whether it is accurate at a high level.
3. Please enrich/correct the baseline workflow with missing steps, handoffs, and decision points before we dive into details.

### Section A - Workflow and Roles
4. Walk through one full TOP3 case from assignment to closure: trigger, actor, tool, output, decision at each step.
5. What exact role boundary exists between PM, Dev, QA, TAM/CSE, and sales/customer-facing roles?
6. In your two-tier team model, what information is Core-only vs All-shared?

### Section B - System Usage and Data Movement
7. For each system (Mantis, Teams, Jenkins, Outlook, InfoSite, Phabricator), what data is read, written, and copied manually?
8. What identifiers link records across systems (ticket ID, branch name, build ID, task ID, image tag)?
9. Where does data duplication happen most often, and what is copied verbatim versus summarized?

### Section C - Pain Points and Impact
10. For the top pain points, provide frequency, severity, and business impact with one concrete recent example.
11. Which delays are most common: investigation, reproduction, build, QA, release, or communication alignment?
12. What failures create the largest customer or business risk?

### Section D - Knowledge Capture and Reuse
13. Which knowledge objects should be captured from each case (symptoms, repro steps, root cause, workaround, fix mapping, release notes)?
14. What format makes captured knowledge reusable in future cases?
15. What quality bar determines whether captured knowledge is trustworthy for reuse?

### Section E - AI-Agent Participation (Step 2 detail)
16. Which actions can the AI agent do autonomously in each phase?
17. Which actions require approval, and who approves them?
18. What escalation conditions should force human takeover?

### Section F - Metrics and Constraints
19. What baseline and target should we set for north-star and each guardrail metric?
20. What security/access/compliance constraints limit agent access per system?
21. What data must remain out of scope for agent ingestion or generation?

## Review Focus Before Step 2
- Confirm/adjust top 4 problems and selected 3.
- Confirm success metrics and preferred target windows.
- Confirm question coverage and wording.
