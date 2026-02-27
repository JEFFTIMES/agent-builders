# State Model - top3-workflow v4.0

## 0. Metadata
- Product: `agent-top3pm`
- Scope: `Top3 case lifecycle objects with workflow-gating states`
- Version: `v4.0`
- Last Updated: `2026-02-26`
- Source Models:
  - `working/agent-top3pm/preview/data-model-core-top3-workflow-v4.0.md`
  - `working/agent-top3pm/preview/data-model-adapter-top3-workflow-v4.0.md`

## 1. State Objects
| Object | State Field | Why State Exists |
| --- | --- | --- |
| `Top3Case` | `status`, `currentPhase` | governs PM decision and cross-team coordination |
| `BranchRequest` | `requestStatus` | approval gating before branch/build operations |
| `BuildRequest` | `requestStatus` | queue/run lifecycle control |
| `BuildRun` | `result` | success/failure gating for validation and release |
| `ValidationRecord` | `validationStatus` | QA acceptance gate |
| `PhabricatorTask` | `status` | QA execution and checklist progress |
| `ReleaseNote` | `publishStatus` | release communication gate |

## 2. State Definitions

### 2.1 `Top3Case.status`
- States: `new`, `investigating`, `fix needed`, `solution sent`, `bumped out`, `released`, `closed`
- Owner: PM
- Trigger sources: Mantis status field, release/close bugnote headlines, build/validation evidence

### 2.2 `Top3Case.currentPhase`
- States: `intake`, `investigation`, `branching`, `building`, `validation`, `release`, `closure`
- Owner: AI agent (derived), PM override allowed
- Trigger sources: action history blocks, branch/build events, validation events, release notes

### 2.3 `BranchRequest.requestStatus`
- States: `draft`, `submitted`, `requested_notified`, `approved`, `rejected`, `cancelled`
- Owner: AI agent + PM
- Trigger sources: Jenkins submit action, branch request email, branch approved/rejected email

### 2.4 `BuildRequest.requestStatus`
- States: `draft`, `submitted`, `queued`, `running`, `succeeded`, `failed`, `cancelled`
- Owner: AI agent + PM/Dev
- Trigger sources: Jenkins build request submit + build notifications

### 2.5 `BuildRun.result`
- States: `running`, `success`, `failure`
- Owner: system/AI sync
- Trigger sources: Jenkins build history and build result emails

### 2.6 `ValidationRecord.validationStatus`
- States: `not_started`, `in_progress`, `passed`, `failed`, `blocked`
- Owner: QA
- Trigger sources: Phabricator comments/checklist and QA evidence updates

### 2.7 `PhabricatorTask.status`
- States: `new`, `assigned`, `investigating`, `build`, `closed_done`
- Owner: QA/Dev
- Trigger sources: task status updates in Phabricator

### 2.8 `ReleaseNote.publishStatus`
- States: `draft`, `ready_for_publish`, `published`
- Owner: PM
- Trigger sources: release note completion + approval

## 3. Transition Table
| Object | From State | To State | Trigger Event | Actor | Guard |
| --- | --- | --- | --- | --- | --- |
| `Top3Case` | `new` | `investigating` | case triage started | PM | case accepted in backlog |
| `Top3Case` | `investigating` | `fix needed` | solution requires code changes | PM | linked issue/NFR exists |
| `Top3Case` | `fix needed` | `solution sent` | validated build delivered | PM | at least one successful build + QA signal |
| `Top3Case` | `solution sent` | `released` | release note finalized | PM | release artifact available |
| `Top3Case` | `released` | `closed` | close/release bugnote posted | PM | closure evidence recorded |
| `BranchRequest` | `draft` | `submitted` | Jenkins request form submitted | AI agent/PM | required fields valid |
| `BranchRequest` | `submitted` | `requested_notified` | branch request email received | AI agent | request email parsed and matched |
| `BranchRequest` | `requested_notified` | `approved` | branch approved email received | AI agent | branch name matched |
| `BranchRequest` | `requested_notified` | `rejected` | branch rejected email received | AI agent | branch name matched |
| `BuildRequest` | `draft` | `submitted` | build with parameters submitted | AI agent/PM/Dev | required payload complete |
| `BuildRequest` | `submitted` | `queued` | Jenkins queue acknowledges request | system | build id allocated |
| `BuildRequest` | `queued` | `running` | build starts | system | executor allocated |
| `BuildRequest` | `running` | `succeeded` | success notification | AI agent/system | result=SUCCESS |
| `BuildRequest` | `running` | `failed` | failure notification | AI agent/system | result=FAILURE |
| `BuildRun` | `running` | `success` | success email + build history | AI agent/system | valid job/build url |
| `BuildRun` | `running` | `failure` | failure email + console url | AI agent/system | valid job/build url |
| `ValidationRecord` | `not_started` | `in_progress` | QA starts checklist | QA | related build exists |
| `ValidationRecord` | `in_progress` | `passed` | checklist complete and no blocker | QA | acceptance criteria met |
| `ValidationRecord` | `in_progress` | `failed` | blocker found | QA | failure evidence provided |
| `ReleaseNote` | `draft` | `ready_for_publish` | note drafted from validation evidence | AI agent/PM | validations summarized |
| `ReleaseNote` | `ready_for_publish` | `published` | PM approval | PM | final content approved |

## 4. AI-Agent State Responsibilities
- Monitor adapter events and keep state transitions deterministic.
- Never transition externally managed states without source evidence.
- Emit escalation when a state is blocked beyond SLA.
- Keep transition audit log (`event`, `old_state`, `new_state`, `source_ref`, `actor`).

## 5. Open Confirmations
| Item | Why Open | Proposed Resolution | Owner |
| --- | --- | --- | --- |
| `Top3Case` `released` vs `closed` separation | some tickets only use status changes without explicit close note | keep both states; close only with release/close bugnote evidence | Peter + HPO |
