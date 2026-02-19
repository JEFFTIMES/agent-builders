# Domain Knowledge - agent-top3pm (v0.1)

Date: 2026-02-18
Source Stage: Step 1 + Step 2 (partial, UC-001 in progress)

## 1. Business Domain Context
- Target role: TOP3 Product Manager (PM).
- Case type currently captured: TOP3 `New Feature Request (NFR)`.
- A second case type (`Bug Fixing`) must be captured separately as UC-002.
- Execution is cross-system and cross-role (PM, Dev, QA, customer-facing stakeholders).

## 2. Core Systems and Purpose
- Mantis: primary case record, ownership/status, requirements, linked tickets, evidence, notes, action history.
- MS Teams: two-tier collaboration (`Core` and `All`) for execution and stakeholder alignment.
- Jenkins: branch/job/build execution.
- Outlook: notification channel for build and stakeholder communications.
- InfoSite: built image/artifact retrieval.
- Phabricator: QA/progress tracking context.
- PMDB: NFR approval/design thread reference.

## 3. As-Is Workflow (captured so far)
### Step 1 - Enter TOP3 case
- Open Mantis list, switch to `Top-3`, apply PM+status filters, open target ticket by ID.
- Shortcut: direct URL by bug_id when known.

### Step 2 - Review case and linked requests
- Read ticket in natural sequence; prioritize newest `Bug Notes` first.
- Open linked NFR/bug tickets in new tabs and review with same rule set.
- Current UC-001 assumption: linked requested bugs are provisional in-scope, then QA reproduces on `Earliest Build Number`; reproducible items are confirmed in-scope.
- For NFR: if PMDB ID appears in linked NFR, fill `PMDB ID` in TOP3.
- Output of Step 2:
  - Draft mandatory `case profile`.
  - Check must-have artifacts: topology, configurations, debug logs.
  - Post Bug Note summarizing available data and requesting missing parts.

## 4. High-value Mantis Field Semantics (UC-001)
Mandatory-first fields include:
- `Category`, ownership/status fields (`Assigned To`, `QA Assignee`, `Assigned PM`, `Status`, `ETA`, etc.)
- `Earliest Build Number`
- `Description`
- `Branch Merge List`
- `Hardware Model`
- `Support/Field Contact`
- `NFR`
- `Attached Files`
- `Affected Customer`
- `PMDB ID`
- `Action History`
- `Bugnote`, `Bug Notes`

Cross-system interpretation highlights:
- Ownership fields drive Teams `Core` composition and filtering in Phabricator/Jenkins/Outlook/Teams.
- `Hardware Model` determines Jenkins image build model selection.
- `Branch Merge List` drives branch/build traceability and requires PM double-confirmation of branchpoint with requester.
- `Description` is the primary requirement scope source, including linked NFR and bundled bug references.

## 5. Reusable Interpreting Rules
- Identity-first: anchor on stable IDs (TOP3/NFR/bug/PMDB).
- State-before-action: check ownership/status/ETA before coordination/build actions.
- Baseline-gating: use `Earliest Build Number` for linked bug reproducibility gating.
- Evidence-driven: rely on `Attached Files`, `Action History`, `Bug Notes` as evidence chain.
- NFR compliance: missing `PMDB ID` is a compliance follow-up trigger.

## 6. Open Items To Capture Later
- UC-001 remaining workflow steps (Step 3+).
- UC-002 bug-fixing-specific inclusion/exclusion rules and deep triage logic.
- Quantitative baselines for defined metrics.
