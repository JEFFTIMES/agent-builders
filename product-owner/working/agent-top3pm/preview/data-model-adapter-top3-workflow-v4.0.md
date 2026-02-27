# Data Model Layer - top3-workflow - Adapter v4.0

## 0. Metadata
- Product: `agent-top3pm`
- Domain / Bounded Context: `Top3 PM cross-system delivery workflow`
- Layer Role: `Interface/Presentation Adapter Layer`
- Version: `v4.0`
- Last Updated (time, owner): `2026-02-26`, `Peter (PD)`
- Source-of-Truth Model File: `working/agent-top3pm/preview/data-model-adapter-top3-workflow-v4.0.md`
- Companion Core File: `working/agent-top3pm/preview/data-model-core-top3-workflow-v4.0.md`

## 1. Coverage Dashboard
| Adapter Object | Source Artifact | Mapping Status | Notes |
| --- | --- | --- | --- |
| `Mantis.Top3CasePage` | `1229978 -- Mantis.html` | Done | Top3 ticket field map |
| `Mantis.NFRPage` | `1225001 -- Mantis.html` | Done | NFR field map |
| `Mantis.IssuePage` | `1241309/1241381/1235578/1249488 -- Mantis.html` | Done | Issue field map |
| `Mantis.BugNoteSection` | Mantis pages | Done | Bugnote extraction map |
| `Mantis.ActionHistorySection` | `1229978 -- Mantis.html` | Done | Top3Build/Patch map |
| `Jenkins.BranchRequestFormPage` | `Jenkins-create-branch.html` | Done | Request form map |
| `Jenkins.BranchProjectPage` | `br_7-4... [Jenkins].html`, `br_7-6... [Jenkins].html` | Done | Branch metadata + build history map |
| `Jenkins.BuildWithParametersPage` | `br_7-4..._build [Jenkins].html` | Done | Build request form map |
| `Outlook.BranchRequestEmail` | `branch-request-email-notify.txt` | Done | Request notification map |
| `Outlook.BranchApprovedEmail` | `branch-approved-email-notify.txt` | Done | Approval notification map |
| `Outlook.BuildNotifyEmail` | build `SUCCESS`/`FAILURE` `.msg` | Done | Build result and URL map |
| `Phabricator.TaskPage` | `⚓ T28209 ... Airtel.html` | Done | Task/checklist/status map |
| `InfoSite.BuildFolderPage` | `InfoSite-b8997.html` | Done | Artifact list map |
| `Teams.CoreThreadView` | `teams-chat-core-1229978.md` | Done | Thread/participant/topic map |

## 2. Adapter Object Cards

### Adapter: `Mantis.Top3CasePage`
- Source: `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html`
- Key fields:
  - `Summary`, `Status`, `Severity`, `ETA`, `Date Submitted`, `Last Update`
  - `Assigned PM`, `Assigned To`, `QA Assignee`, `Support/Field Contact`
  - `Branch Merge List`, `Hardware Model`, `NFR`, `Affected Customer`
- Core mapping:
  - `Top3Case.title <- Summary`
  - `Top3Case.status <- Status`
  - `Top3Case.severity <- Severity`
  - `Top3Case.eta <- ETA`
  - `Top3Case.pmOwnerId <- Assigned PM`
  - `Top3Case.devOwnerId <- Assigned To`
  - `Top3Case.qaOwnerId <- QA Assignee`
  - `Top3Case.branch <- Branch Merge List`
  - `Top3Case.hardwareModels <- Hardware Model`
  - `Top3Case.hasNFR <- NFR ids`
- AI-agent actions:
  - `read_page_fields`, `normalize_person_ids`, `upsert_top3case_snapshot`.

### Adapter: `Mantis.NFRPage`
- Source: `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html`
- Key fields:
  - `Product`, `Solution`, `Summary`, `Description`, `Status`, `Severity`
  - `Reporter`, `Date Submitted`, `Last Update`, `ETA`
  - `Salesforce Opp IDs`, `Salesforce Account IDs`, `Additional Information`, `Affected Customer`
- Core mapping:
  - `NFR.product <- Product`
  - `NFR.solutionRaw <- Solution`
  - `NFR.summary <- Summary`
  - `NFR.description <- Description`
  - `NFR.status <- Status`
  - `NFR.reporterId <- Reporter`
  - `NFR.salesforceOppIds <- Salesforce Opp IDs`
  - `NFR.salesforceAccountIds <- Salesforce Account IDs`
- AI-agent actions:
  - `sync_nfr_fields`, `normalize_salesforce_lists`, `extract_nfr_links_from_notes`.

### Adapter: `Mantis.IssuePage`
- Source:
  - `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`
  - `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html`
  - `working/agent-top3pm/assets/top3apps/1235578 -- Mantis.html`
  - `working/agent-top3pm/assets/top3apps/1249488 -- Mantis.html`
- Key fields:
  - `Category`, `Severity`, `Reproducibility`, `Summary`, `Description`, `Status`, `Resolution`
  - `Reporter`, `Assigned To`, `QA Assignee`, `Reproduced By`, `ETA`, `Duplicate ID`
  - `Dev QA Status`, `resolved in`
- Core mapping:
  - `Issue.category <- Category`
  - `Issue.severity <- Severity`
  - `Issue.reproducibility <- Reproducibility`
  - `Issue.title <- Summary`
  - `Issue.description <- Description`
  - `Issue.status <- Status`
  - `Issue.assignedDevId <- Assigned To`
  - `Issue.qaAssigneeId <- QA Assignee`
  - `Issue.reproducedById <- Reproduced By`
  - `Issue.duplicateIssueId <- Duplicate ID`
  - `Issue.resolvedIn <- resolved in/Resolution context`
- AI-agent actions:
  - `sync_issue_fields`, `extract_issue_cross_refs`, `compose_issue_conclusion`.

### Adapter: `Mantis.BugNoteSection`
- Source: Bug Notes blocks in Mantis pages above
- Key fields: author, created time, note body, reply chain
- Core mapping:
  - `BugNote.authorId <- note author`
  - `BugNote.createdAt <- note timestamp`
  - `BugNote.rawText <- note body`
  - `Top3Case|NFR|Issue.hasBugNote <- note parent`
- AI-agent actions:
  - `extract_bugnotes`, `classify_headline`, `extract_ticket_mentions`, `extract_phab_mentions`.

### Adapter: `Mantis.ActionHistorySection`
- Source: `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html`
- Key fields: `ID`, `Merged`, `Summary`; `Top3Build` row markers
- Core mapping:
  - `ActionHistory.parentCaseId <- top3 case id`
  - `Top3Build.buildNumber <- Top3Build row`
  - `Patch.issueId <- fix row ID`
  - `Patch.mainBranchPointRaw <- Merged`
  - `Patch.issueTitle <- Summary`
- AI-agent actions:
  - `segment_build_groups`, `materialize_top3build_and_patch_records`.

### Adapter: `Jenkins.BranchRequestFormPage`
- Source: `working/agent-top3pm/assets/top3apps/Jenkins-create-branch.html`
- Key fields:
  - `GITLAB_TOP_LEVEL_GROUP`, `GITLAB_PROJECT`, `MAJOR_VERSION`, `MINOR_VERSION`
  - `NEW_BRANCH_NAME`, `BRANCH_POINT`, `BRANCH_TYPE`, `BRANCH_DESCRIPTION`
  - `FOR_CUSTOMER`, `DEV_LEAD`, `PM_LEAD`
- Core mapping:
  - field payload -> `BranchRequest` draft payload
  - post-submit request -> `BranchRequest`
- AI-agent actions:
  - `open_form`, `fill_form`, `validate_required_values`, `submit_request`.

### Adapter: `Jenkins.BranchProjectPage`
- Source:
  - `working/agent-top3pm/assets/top3apps/br_7-4_logging_rsso_acc_msgs_38kg [Jenkins].html`
  - `working/agent-top3pm/assets/top3apps/br_7-6_stp_fail_dual_intercross_conn [Jenkins].html`
- Key fields:
  - Branch metadata: `Dev Lead`, `PM Lead`, `Branch Type`, `Description`, `Created At`, `Initial Branch Point`, `Repository URL`
  - Build history cards: build tag/time/trigger/description snippets
- Core mapping:
  - branch metadata -> `Branch`
  - build history entries -> `BuildRun`
- AI-agent actions:
  - `sync_branch_metadata`, `crawl_build_history`, `link_buildrun_to_branch`.

### Adapter: `Jenkins.BuildWithParametersPage`
- Source: `working/agent-top3pm/assets/top3apps/br_7-4_logging_rsso_acc_msgs_38kg_build [Jenkins].html`
- Key fields:
  - `GIT_PROJECT`, `GIT_REF`, `GITLAB_URL`, `BRANCH_TYPE`, `PATCH_VERSION`
  - `BUILD_LABEL`, `BUILD_LABEL_OTHER`, `EMAIL_RECIPIENTS`, `BUILD_DESCRIPTION`
  - `MATRIX` combinations
  - `RUN_DB_UPDATE`, `APPEND_MODEL_NAME`, `TRIAL`, `MATURITY`, `SA`, `SA_PATCH_VERSION`
- Core mapping:
  - form payload -> `BuildRequest` and `BuildRequest.selectedCombinations`
- AI-agent actions:
  - `prepare_build_request`, `select_matrix_targets`, `submit_build`, `record_buildrequest_id`.

### Adapter: `Outlook.BranchRequestEmail`
- Source: `working/agent-top3pm/assets/top3apps/branch-request-email-notify.txt`
- Key fields:
  - title, `Branch Name`, `Project`, `Version`, `BranchType`, `Branch Point`
  - `Requestor`, `Dev Lead`, `PM Lead`, `For Customer`, `Description(top3CaseId)`
- Core mapping:
  - body fields -> `BranchRequest`
  - notification -> `EmailNotify(eventType=jenkins-branch-requested)`
- AI-agent actions:
  - `parse_request_email`, `upsert_branchrequest`, `link_request_to_case`.

### Adapter: `Outlook.BranchApprovedEmail`
- Source: `working/agent-top3pm/assets/top3apps/branch-approved-email-notify.txt`
- Key fields: title, first `Source Code URL`, `Jenkins Job URL`
- Core mapping:
  - `BranchRequest.branchSourceCodeUrl`, `BranchRequest.branchJobUrl`
  - `EmailNotify(eventType=jenkins-branch-approved)`
- AI-agent actions:
  - `parse_approval_email`, `set_request_status_approved`, `enrich_branch_links`.

### Adapter: `Outlook.BuildNotifyEmail`
- Source:
  - `working/agent-top3pm/assets/top3apps/FortiOS v7# 7.6.4.8361 - FAILURE!.msg`
  - `working/agent-top3pm/assets/top3apps/FortiOS - br_7-6_stp_fail_dual_intercross_conn - Build 7.6.4.8362 - SUCCESS!.msg`
- Key fields:
  - failure mail: `Subject`, `Date`, `From`, `To`, `X-Jenkins-Job`, `X-Jenkins-Result`, console URL
  - success mail: `Subject`, `Date`, recipients, console URL, InfoSite URL
- Core mapping:
  - `BuildRun.result`, `BuildRun.consoleOutputUrl`, `BuildRun.imagesStoreAt`, `BuildRun.notifiedAt`
  - `EmailNotify(eventType=jenkins-building-success|failure)`
- AI-agent actions:
  - `classify_build_result`, `extract_urls`, `upsert_buildrun_status`.

### Adapter: `Phabricator.TaskPage`
- Source: `working/agent-top3pm/assets/top3apps/⚓ T28209 Top3 1229978_ RADIUS extensions for Bharti Airtel.html`
- Key fields:
  - task id/title/status/tags/subscribers/assignee/author
  - Mantis IDs, test checklist, timeline comments
- Core mapping:
  - `PhabricatorTask.*`
  - `ValidationRecord` seeds from checklist and QA notes
- AI-agent actions:
  - `sync_task_snapshot`, `extract_timeline_delta`, `collect_checklist_links`.

### Adapter: `InfoSite.BuildFolderPage`
- Source: `working/agent-top3pm/assets/top3apps/InfoSite-b8997.html`
- Key fields:
  - folder path with branch/build tag
  - table rows: name, last modified, size, action links (`View`, `Download`)
- Core mapping:
  - one row -> one `BuildArtifact`
  - folder metadata -> `BuildRun.imagesStoreAt`
- AI-agent actions:
  - `crawl_artifact_rows`, `normalize_artifact_type`, `attach_to_top3build`.

### Adapter: `Teams.CoreThreadView`
- Source: `working/agent-top3pm/assets/top3apps/teams-chat-core-1229978.md`
- Key fields:
  - topic (`TOP3 1229978 Airtel`)
  - participant list and timestamped chat messages
  - links to branch and phab task
- Core mapping:
  - `CommunicationThread.topic`, `CommunicationThread.lastMessageAt`
  - participants -> `Person`
  - referenced links -> related `Top3Case`, `Branch`, `PhabricatorTask`
- AI-agent actions:
  - `summarize_thread`, `extract_action_items`, `extract_new_participants`.

## 2.1 Core/Adapter Mapping Table
| Adapter Object | Adapter Field | Canonical Property | Read/Write Policy | Confirmation |
| --- | --- | --- | --- | --- |
| `Mantis.Top3CasePage` | `Status` | `Top3Case.status` | `agent_managed` | Confirmed |
| `Mantis.Top3CasePage` | `Branch Merge List` | `Top3Case.branch` (snapshot), `Top3Case.usesBranch` | `agent_managed` | Confirmed |
| `Mantis.NFRPage` | `Solution` | `NFR.solutionRaw` | `agent_managed` | Confirmed |
| `Mantis.IssuePage` | `Assigned To` | `Issue.assignedDevId` | `agent_managed` | Confirmed |
| `Mantis.BugNoteSection` | note body | `BugNote.rawText` | `system_sync_only` | Confirmed |
| `Mantis.ActionHistorySection` | `Top3Build` row | `Top3Build.buildNumber` | `system_sync_only` | Confirmed |
| `Jenkins.BranchRequestFormPage` | `NEW_BRANCH_NAME` | `BranchRequest.branchName` | `agent_managed` | Confirmed |
| `Jenkins.BranchRequestFormPage` | `BRANCH_POINT` | `BranchRequest.branchPoint` | `agent_managed` | Confirmed |
| `Jenkins.BuildWithParametersPage` | `GIT_REF` | `BuildRequest.gitRef` | `agent_managed` | Confirmed |
| `Jenkins.BuildWithParametersPage` | `MATRIX` | `BuildRequest.selectedCombinations` | `agent_managed` | Confirmed |
| `Outlook.BranchRequestEmail` | `Description` | `BranchRequest.top3CaseId` | `system_sync_only` | Confirmed |
| `Outlook.BranchApprovedEmail` | `Jenkins Job URL` | `BranchRequest.branchJobUrl` | `system_sync_only` | Confirmed |
| `Outlook.BuildNotifyEmail` | success/failure subject/body | `BuildRun.result` | `system_sync_only` | Confirmed |
| `Outlook.BuildNotifyEmail` | console URL | `BuildRun.consoleOutputUrl` | `system_sync_only` | Confirmed |
| `InfoSite.BuildFolderPage` | file row | `BuildArtifact.*` | `system_sync_only` | Confirmed |
| `Phabricator.TaskPage` | `Status` | `PhabricatorTask.status` | `system_sync_only` | Confirmed |
| `Teams.CoreThreadView` | message stream | `CommunicationThread.lastMessageAt` | `system_sync_only` | Confirmed |

## 3. Adapter Interaction Rules
1. AI agent always maps adapter records to canonical IDs before any writeback.
2. Adapter field changes never redefine canonical semantics.
3. For write actions, AI agent executes policy checks from automation model first.
4. Adapter failures produce `sync_error` events and keep canonical state unchanged.

## 4. Open Confirmations
| Item | Why Open | Proposed Resolution | Owner |
| --- | --- | --- | --- |
| Standard parser for Outlook `.msg` | current extraction is regex-based and noisy | implement dedicated parser in engineering phase | Peter + HPO |
| Cross-site branch URL normalization (`van/ott/sjc`) | multi-URL variants in approvals | keep first URL as canonical, store full list in adapter cache | Peter + HPO |

## 5. Decision Log
- [2026-02-26] Added missing adapter-layer coverage for Mantis/Jenkins/Outlook/Teams/Phabricator/InfoSite objects.
- [2026-02-26] Split page/form/email objects from canonical entities and formalized field-level mapping contracts.
