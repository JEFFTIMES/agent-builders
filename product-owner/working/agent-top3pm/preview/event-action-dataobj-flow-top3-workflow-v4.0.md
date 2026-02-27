# Interaction Model - Event Action DataObject Flow - top3-workflow v4.0

## 0. Metadata
- Product: `agent-top3pm`
- Version: `v4.0`
- Last Updated: `2026-02-26`
- Scope: `cross-application PM workflow from case intake to release/closure`
- Related Models:
  - `working/agent-top3pm/preview/data-model-core-top3-workflow-v4.0.md`
  - `working/agent-top3pm/preview/data-model-adapter-top3-workflow-v4.0.md`
  - `working/agent-top3pm/preview/state-model-top3-workflow-v4.0.md`

## 1. Canonical Event Flow
| Seq | Event | Actor | Action | Data Object | Emitted Event | Evidence Source |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | `top3_case_created_or_opened` | PM | open Top3 ticket and set baseline fields | `Mantis.Top3CasePage` -> `Top3Case` | `top3_case_snapshot_synced` | `1229978 -- Mantis.html` |
| 2 | `nfr_linked_to_case` | PM/QA | link NFR ID in case | `Mantis.Top3CasePage` -> `Top3Case.hasNFR` | `nfr_snapshot_required` | `1229978 -- Mantis.html` (`NFR` field) |
| 3 | `nfr_snapshot_required` | AI agent | pull NFR fields and normalize | `Mantis.NFRPage` -> `NFR` | `nfr_synced` | `1225001 -- Mantis.html` |
| 4 | `issue_linked_to_case` | PM/QA | link issue IDs and owners | `Mantis.IssuePage` -> `Issue` | `issue_snapshot_required` | issue pages in `top3apps` |
| 5 | `issue_snapshot_required` | AI agent | sync issue fields and references | `Mantis.IssuePage` -> `Issue` | `issue_synced` | issue pages in `top3apps` |
| 6 | `bugnote_added` | PM/Dev/QA | add note in ticket | `Mantis.BugNoteSection` -> `BugNote` | `bugnote_parsed` | Mantis bug notes |
| 7 | `bugnote_parsed` | AI agent | parse mentions/headline and relation links | `BugNote` | `cross_ticket_links_updated` | Mantis notes + parsing rules |
| 8 | `teams_discussion_updated` | PM/Dev/QA | discuss blockers and action owners | `Teams.CoreThreadView` -> `CommunicationThread` | `thread_digest_updated` | `teams-chat-core-1229978.md` |
| 9 | `phab_task_updated` | QA/Dev | update QA task, checklist, comments | `Phabricator.TaskPage` -> `PhabricatorTask` | `validation_update_required` | `⚓ T28209 ... Airtel.html` |
| 10 | `branch_request_prepared` | AI agent | fill Jenkins request form with case context | `Jenkins.BranchRequestFormPage` | `branch_request_submitted` | `Jenkins-create-branch.html` |
| 11 | `branch_request_submitted` | Jenkins | accept request payload | `BranchRequest` | `branch_request_email_received` | branch request email |
| 12 | `branch_request_email_received` | AI agent | parse request notify and upsert request | `Outlook.BranchRequestEmail` -> `BranchRequest` | `branch_request_state_requested` | `branch-request-email-notify.txt` |
| 13 | `branch_approved_email_received` | AI agent | parse approval notify and enrich links | `Outlook.BranchApprovedEmail` -> `BranchRequest` | `branch_request_state_approved` | `branch-approved-email-notify.txt` |
| 14 | `branch_metadata_sync` | AI agent | sync branch project page details | `Jenkins.BranchProjectPage` -> `Branch` | `branch_ready_for_build` | branch project HTML pages |
| 15 | `build_request_prepared` | AI agent/PM/Dev | fill build parameters + matrix combinations | `Jenkins.BuildWithParametersPage` -> `BuildRequest` | `build_request_submitted` | `br_7-4..._build [Jenkins].html` |
| 16 | `build_started` | Jenkins | run build pipeline | `BuildRun` | `build_result_notified` | Jenkins build history + notifications |
| 17 | `build_success_notified` | AI agent | parse success mail and set run success | `Outlook.BuildNotifyEmail` -> `BuildRun` | `artifact_index_required` | success `.msg` |
| 18 | `build_failure_notified` | AI agent | parse failure mail and set run failure | `Outlook.BuildNotifyEmail` -> `BuildRun` | `failure_escalation_required` | failure `.msg` |
| 19 | `artifact_index_required` | AI agent | crawl InfoSite folder and ingest artifacts | `InfoSite.BuildFolderPage` -> `BuildArtifact` | `artifacts_synced` | `InfoSite-b8997.html` |
| 20 | `validation_update_required` | QA + AI agent | collect checklist and test evidence | `ValidationRecord` | `validation_passed_or_failed` | Phabricator comments/checklist |
| 21 | `validation_passed_or_failed` | PM + AI agent | update case state and draft release note | `Top3Case`, `ReleaseNote` | `release_note_ready` | Mantis + Phab + build evidence |
| 22 | `release_note_ready` | PM | publish release note / release bugnote | `ReleaseNote`, `BugNote` | `case_released_or_closed` | Mantis bug note + RN fields |
| 23 | `case_released_or_closed` | AI agent | derive closure fields and finalize snapshot | `Top3Case.closedAt`, `Top3Case.closureReason` | `workflow_cycle_completed` | release/close bugnote evidence |

## 2. Interaction Chains by Objective

### 2.1 Branch Creation Chain
`Top3Case` -> `Jenkins.BranchRequestFormPage` -> `BranchRequest` -> `Outlook.BranchRequestEmail` -> `Outlook.BranchApprovedEmail` -> `Branch`

### 2.2 Build and Artifact Chain
`Branch` -> `Jenkins.BuildWithParametersPage` -> `BuildRequest` -> `BuildRun` -> `Outlook.BuildNotifyEmail` -> `InfoSite.BuildFolderPage` -> `BuildArtifact`

### 2.3 Validation and Release Chain
`Issue` + `PhabricatorTask` + `ValidationRecord` -> `ReleaseNote` -> `BugNote(---Release---/---Close---)` -> `Top3Case`

## 3. Event Normalization Rules
1. `EmailNotify.eventType` must be normalized before any state transition.
2. Branch request events are matched by normalized `branchName` first, then by recency.
3. Build result events are matched by Jenkins build URL/build number.
4. Bugnote mention extraction uses strict ticket id patterns (`#id`, `mantis#id`, `bug_id=id`).
5. AI-derived events (`compose_*`, `derive_*`) require source references attached to event payload.

## 4. AI-Agent Responsibilities in Interaction Layer
- Detect, classify, and route cross-app events to canonical entities.
- Execute only policy-allowed actions; escalate when approval is required.
- Keep idempotent upsert behavior for repeated notifications.
- Emit clear recovery actions for failed branch/build transitions.

## 5. Open Confirmations
| Item | Why Open | Proposed Resolution | Owner |
| --- | --- | --- | --- |
| Build failure notification payload schema | failure `.msg` has mixed embedded logs | normalize with parser and keep current URL extraction fallback | Peter + HPO |
