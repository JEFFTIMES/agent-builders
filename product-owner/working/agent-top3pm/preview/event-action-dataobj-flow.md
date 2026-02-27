# Event-Action-DataObject Flow (Draft)

## 1. Data Objects

- `Jenkins.BranchRequestPage`
- `Mantis.Top3CasePage`
- `Outlook.EmailNotify`
- `user.pm`

## 2. Actions

- `updatesTop3BranchName`
- `clickSubmitButton`
- `fillForm`
- `checkEmail`
- `checkBranchName`

## 3. Events

- `branchApproved`
- `branchNotExist`
- `caseStart`
- `regularlyCheckEmail`
- `branchNameReady`
- `branchNameUpdated`

## 4. Flow

1. `(caseStart)` [triggers] `<user.pm>` [checkBranchName] on `<Mantis.Top3CasePage>` [emits] `(branchNotExist)`
2. `(branchNotExist)` [triggers] `<user.pm>` [fillForm] on `<Jenkins.BranchRequestPage>` AND [clickSubmitButton] on `<Jenkins.BranchRequestPage>` [emits] `(branchApproved)`
3. `(regularlyCheckEmail)` [triggers] `<user.pm>` [checkEmail] `<Outlook.EmailNotify>` which [carries] `(branchApproved)` [emits] `(branchNameReady)`
4. `(branchNameReady)` [triggers] `<user.pm>` [updatesTop3BranchName] in `<Mantis.Top3CasePage>` [emits] `(branchNameUpdated)`

## 5. Quick Notes

- Naming cleanup candidate: `branchApproved` is currently emitted right after submit in step 2, but semantically it is usually emitted by an approval email/system response after submit.
- Keep current draft as-is for discussion; normalize event timing in next refinement.

---

## 6. Refined Flow (Peter)

### 6.1 Data Objects

- `Jenkins.BranchRequestPage`
- `Mantis.Top3CasePage`
- `Outlook.EmailNotify`
- `user.pm`

### 6.2 Actions

- `checkBranchName`
- `fillForm`
- `clickSubmitButton`
- `checkEmail`
- `updatesTop3BranchName`

### 6.3 Events

- `caseStart`
- `branchNotExist`
- `branchRequestSubmitted`
- `regularlyCheckEmail`
- `branchApproved`
- `branchNameReady`
- `branchNameUpdated`

### 6.4 Refined Flow Definition

1. `(caseStart)` [triggers] `<user.pm>` [checkBranchName] on `<Mantis.Top3CasePage>` [emits] `(branchNotExist)`
2. `(branchNotExist)` [triggers] `<user.pm>` [fillForm] on `<Jenkins.BranchRequestPage>` AND [clickSubmitButton] on `<Jenkins.BranchRequestPage>` [emits] `(branchRequestSubmitted)`
3. `(regularlyCheckEmail)` [triggers] `<user.pm>` [checkEmail] on `<Outlook.EmailNotify>`; when email [carries] `(branchApproved)`, [emits] `(branchNameReady)`
4. `(branchNameReady)` [triggers] `<user.pm>` [updatesTop3BranchName] on `<Mantis.Top3CasePage>` [emits] `(branchNameUpdated)`

### 6.5 Refinement Note

- `branchApproved` is modeled as a downstream event carried by email/system response after submit, instead of being emitted immediately at submit time.

---

## 7. State Modeling Discussion (HPO + Peter)

### 7.1 Event Meaning

- In this design, an `event` represents a meaningful state change (or detected state transition) in the workflow.
- Events trigger next actions.

### 7.2 Should States Attach to Objects?

- Yes. States should attach to a concrete object/entity, not float globally.

### 7.3 Candidate Object States for This Case

1. `Top3Case`
- `branchLinkState`: `missing` -> `present`
- Derived from whether `Top3Case.branch` is empty or populated.

2. `BranchRequest`
- `requestState`: `not_requested` -> `submitted` -> `approved` / `rejected`
- `syncState`: `awaiting_email` -> `email_matched` -> `applied_to_case`

3. `EmailNotify`
- `parseState`: `unseen` -> `seen` -> `parsed` -> `matched_to_request`

### 7.4 Event as Transition Examples

- `branchNotExist`: `Top3Case.branchLinkState` becomes `missing`
- `branchRequestSubmitted`: `BranchRequest.requestState` becomes `submitted`
- `branchApproved`: `BranchRequest.requestState` becomes `approved` (from matched email)
- `branchNameUpdated`: `Top3Case.branchLinkState` becomes `present`

### 7.5 Rules for Defining States

Do not define state for every changeable property. Define state only when all/most apply:

1. Workflow-relevant
- It gates next actions or decisions.

2. Multi-step
- It has meaningful staged progression, not simple value overwrite.

3. Operationally useful
- Needed for automation, monitoring, retries, or SLA handling.

4. Stable and finite
- Small enum with clear transition semantics.

5. Auditable
- Helps explain current stage and transition path.

### 7.6 Practical Modeling Pattern

- Keep business data as properties (e.g., `branchName`, `title`).
- Add a small number of lifecycle states per object (e.g., `requestState`, `syncState`, `parseState`).
- Record detailed mutations in interaction/event logs (`WorkflowInteractionEvent`) instead of creating too many state enums.

---

## 8. Two-Layer Data Modeling Direction (HPO Proposal + Peter Summary)

### 8.1 HPO Thinking (Captured)

- Split data modeling into two layers (or two perspectives):
1. A `core` layer that abstracts stable business entities.
2. A `presentation` layer whose entities:
   - map business-entity properties to current human-application interface fields (e.g., `Mantis.Top3CasePage`, `Jenkins.BranchRequestPage`), and
   - guide upcoming agent interactions with applications in a human-mimic way.

### 8.2 Peter Summary

- This direction is correct and practical.
- Recommended structure:
1. `Canonical/Core Domain Layer`
   - stable entities and relations (`Top3Case`, `BranchRequest`, `Branch`, etc.)
2. `Interface/Presentation Adapter Layer`
   - app/page specific contracts (`Mantis.Top3CasePage`, `Jenkins.BranchRequestPage`, `Outlook.EmailNotify`)
   - field mapping between app interface and canonical properties
   - interaction guidance for agent operations

### 8.3 Industry Pattern Equivalents

- `Canonical Data Model + Anti-Corruption Layer`
- `Domain Model + View/DTO Model`
- `Page Object Model` (test/RPA style)
- `System-of-Record + Integration Adapter`

### 8.4 Why It Works for Agentic Systems

- prevents UI volatility from polluting core domain model
- allows per-application agent automation to evolve independently
- makes read/write boundaries and rationale explicit for agent behavior

### 8.5 Guardrails

1. Keep business meaning in the core layer.
2. Keep selectors, page fields, and interaction steps in the interface layer.
3. Do not leak temporary app-specific fields into canonical entities unless they are real business facts.
4. Define explicit mapping rules and ownership for each mapped field.
5. Tie interface-layer write actions to `external_data_touch_policy`.

---

## 9. v3.3 Canonical Interaction Slice (AI-Agent First)

- Canonical artifact: `working/agent-top3pm/preview/workflow-interaction-events-v3.3.md`
- Actor default for operational execution: `AI-Agent`
- Human role: PM confirms exception paths (`agent_suggest_only` or low-confidence match cases)

### 9.1 Canonical Sequence

1. `(caseStart)` -> `<AI-Agent>` checks `Mantis.Top3CasePage.branch` and emits `(branchNotExist)` if empty.
2. `(branchNotExist)` -> `<AI-Agent>` fills/submits `Jenkins.BranchRequestPage` and emits `(branchRequestSubmitted)`.
3. `(regularlyCheckEmail)` -> `<AI-Agent>` parses `Outlook.EmailNotify` and emits `(emailParsed)`.
4. `(emailParsed)` -> `<AI-Agent>` matches email to `BranchRequest` and emits `(branchApproved|branchRejected)`.
5. `(branchApproved)` -> `<AI-Agent>` updates `Mantis.Top3CasePage.branch` and emits `(branchNameUpdated)`.
