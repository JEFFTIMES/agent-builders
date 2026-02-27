# Data Model Layer - top3-workflow - Core v4.0

## 0. Metadata
- Product: `agent-top3pm`
- Domain / Bounded Context: `Top3 PM cross-system delivery workflow`
- Discovery Scope Boundary: `canonical business entities and relations across Mantis, Jenkins, Outlook, Teams, Phabricator, InfoSite`
- Entity Lifecycle Span (start -> end): `Top3 case intake -> branch/build -> validation -> release/closure`
- Version: `v4.0`
- Last Updated (time, owner): `2026-02-26`, `Peter (PD)`
- Source Corpus Snapshot:
  - `working/agent-top3pm/assets/JD.md`
  - `working/agent-top3pm/assets/top3-case-category.md`
  - `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html`
  - `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html`
  - `working/agent-top3pm/assets/top3apps/1235578 -- Mantis.html`
  - `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`
  - `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html`
  - `working/agent-top3pm/assets/top3apps/1249488 -- Mantis.html`
  - `working/agent-top3pm/assets/top3apps/Jenkins-create-branch.html`
  - `working/agent-top3pm/assets/top3apps/br_7-4_logging_rsso_acc_msgs_38kg [Jenkins].html`
  - `working/agent-top3pm/assets/top3apps/br_7-4_logging_rsso_acc_msgs_38kg_build [Jenkins].html`
  - `working/agent-top3pm/assets/top3apps/br_7-6_stp_fail_dual_intercross_conn [Jenkins].html`
  - `working/agent-top3pm/assets/top3apps/branch-request-email-notify.txt`
  - `working/agent-top3pm/assets/top3apps/branch-approved-email-notify.txt`
  - `working/agent-top3pm/assets/top3apps/FortiOS v7# 7.6.4.8361 - FAILURE!.msg`
  - `working/agent-top3pm/assets/top3apps/FortiOS - br_7-6_stp_fail_dual_intercross_conn - Build 7.6.4.8362 - SUCCESS!.msg`
  - `working/agent-top3pm/assets/top3apps/InfoSite-b8997.html`
  - `working/agent-top3pm/assets/top3apps/teams-chat-core-1229978.md`
  - `working/agent-top3pm/assets/top3apps/⚓ T28209 Top3 1229978_ RADIUS extensions for Bharti Airtel.html`
- Source-of-Truth Model File: `working/agent-top3pm/preview/data-model-core-top3-workflow-v4.0.md`
- Companion Adapter File: `working/agent-top3pm/preview/data-model-adapter-top3-workflow-v4.0.md`

## 1. Coverage Dashboard
| Core Entity | Property Check | Relation Check | Status | Open Items |
| --- | --- | --- | --- | --- |
| ProductFamily | Done | Done | Reviewed | None |
| Top3Case | Done | Done | Reviewed | None |
| NFR | Done | Done | Reviewed | None |
| Issue | Done | Done | Reviewed | None |
| BugNote | Done | Done | Reviewed | None |
| ActionHistory | Done | Done | Reviewed | None |
| Top3Build | Done | Done | Reviewed | None |
| Patch | Done | Done | Reviewed | None |
| BranchRequest | Done | Done | Reviewed | None |
| Branch | Done | Done | Reviewed | None |
| BuildRequest | Done | Done | Reviewed | None |
| BuildRun | Done | Done | Reviewed | None |
| BuildArtifact | Done | Done | Reviewed | New in v4.0 |
| ValidationRecord | Done | Done | Reviewed | None |
| ReleaseNote | Done | Done | Reviewed | None |
| PhabricatorTask | Done | Done | Reviewed | None |
| EmailNotify | Done | Done | Reviewed | None |
| Person | Done | Done | Reviewed | None |
| Team | Done | Done | Reviewed | None |
| CommunicationThread | Done | Done | Reviewed | None |

## 2. Entity Discovery Cards (Canonical Core)

### Entity: `ProductFamily`
- Properties: `productFamilyId`, `name`
- Outgoing relations: `hasBranch -> Branch (0..*)`, `hasCase -> Top3Case (0..*)`
- Property source: branch/ticket product values in Jenkins and Mantis pages.

### Entity: `Top3Case`
- Properties:
  - `caseId`, `title`, `caseCategory`, `status`, `severity`, `eta`, `createdAt`, `updatedAt`, `description`
  - `customerName`, `pmOwnerId`, `qaOwnerId`, `devOwnerId`, `supportOwnerIds`
  - `branch` (snapshot), `hardwareModels`, `caseSummary` (AI composed), `currentPhase`, `closedAt`, `closureReason` (AI composed)
- Outgoing relations:
  - `hasNFR -> NFR`, `hasIssue -> Issue`, `hasBugNote -> BugNote`, `hasActionHistory -> ActionHistory`
  - `usesBranch -> Branch`, `publishesDeliverable -> ReleaseNote`, `hasTeam -> Team`
  - `ownedBy/hasQaOwner/hasDevOwner/hasSupportOwner -> Person`
- Incoming relations: `raisedForTop3Case <- BranchRequest`, `linkedTo <- PhabricatorTask`
- Property source:
  - Mantis `1229978 -- Mantis.html`: summary/status/severity/eta/owners/branch/hardware/customer/time fields.
  - `bugnote-headlines.md` + Mantis bugnotes: closure signal.
  - AI fields: `caseSummary`, `closureReason` from cross-app evidence.
- AI-agent actions:
  - `sync_case_snapshot`, `derive_phase`, `compose_case_summary`, `compose_closure_reason`.

### Entity: `NFR`
- Properties:
  - `nfrId`, `product`, `solutionRaw`, `solutionTags`, `summary`, `description`, `status`, `severity`
  - `reporterId`, `dateSubmitted`, `lastUpdate`, `eta`, `affectedCustomer`
  - `salesforceOppIds`, `salesforceAccountIds`, `additionalInformation`
  - `duplicateNfrId`, `referredNfrIds`, `referredIssueIds`, `conclusion` (AI composed)
- Outgoing relations: `reportedBy -> Person`, `hasBugNote -> BugNote`, `duplicateOf -> NFR`, `references -> NFR|Issue`
- Incoming relations: `hasNFR <- Top3Case`, `references <- BugNote`
- Property source: Mantis `1225001 -- Mantis.html` plus bugnote parsing.
- AI-agent actions: `normalize_solution_tags`, `extract_cross_ticket_links`, `compose_nfr_conclusion`.

### Entity: `Issue`
- Properties:
  - `issueId`, `title`, `description`, `status`, `severity`, `category`, `reproducibility`
  - `reporterId`, `assignedDevId`, `qaAssigneeId`, `reproducedById`
  - `reportedVersion`, `earliestBuildNumber`, `resolvedIn`, `fixSchedule`, `duplicateIssueId`
  - `referredIssueIds`, `referredNfrIds`, `conclusion` (AI composed)
- Outgoing relations: `reportedBy -> Person`, `assignedTo -> Person(Dev)`, `qaAssignedTo -> Person(QA)`, `hasBugNote -> BugNote`, `references -> Issue|NFR`
- Incoming relations: `hasIssue <- Top3Case`, `fixesIssue <- Patch`, `validatesIssue <- ValidationRecord`
- Property source: Mantis issue pages (`1241309`, `1241381`, `1235578`, `1249488`) plus cross-ticket note parsing.
- AI-agent actions: `extract_issue_links`, `compose_issue_conclusion`.

### Entity: `BugNote`
- Properties: `bugNoteId`, `parentTicketId`, `authorId`, `createdAt`, `headlineRaw`, `headlineType`, `rawText`, `mentionedTicketIds`, `phabTaskIds`, `extractAt`
- Outgoing relations: `authoredBy -> Person`, `references -> Top3Case|NFR|Issue|PhabricatorTask`
- Incoming relations: `hasBugNote <- Top3Case|NFR|Issue`
- Property source: Mantis Bug Notes sections and parsing rules.
- AI-agent actions: `parse_headline`, `extract_mentions`, `link_phab_tasks`.

### Entity: `ActionHistory`
- Properties: `actionHistoryId`, `parentCaseId`, `sourceSystem`, `sourceSection`
- Outgoing relations: `hasTop3Build -> Top3Build`
- Incoming relations: `hasActionHistory <- Top3Case`
- Property source: Mantis `Action History` table in `1229978`.
- AI-agent actions: `group_rows_into_build_blocks`.

### Entity: `Top3Build`
- Properties: `top3BuildId`, `buildNumber`, `buildQualifier`, `sequenceNo`, `sourceSystem`
- Outgoing relations: `hasPatch -> Patch`, `publishesArtifact -> BuildArtifact`
- Incoming relations: `hasTop3Build <- ActionHistory`, `publishesTop3Build <- BuildRun`
- Property source: Mantis Action History `Top3Build` rows and Jenkins/InfoSite build refs.
- AI-agent actions: `upsert_top3build`, `link_to_buildrun`.

### Entity: `Patch`
- Properties: `patchId`, `top3BuildId`, `issueId`, `issueTitle`, `mainBranchPointRaw`, `mainBranchPoint`, `mergeToMainState`, `sequenceNo`
- Outgoing relations: `fixesIssue -> Issue`
- Incoming relations: `hasPatch <- Top3Build`
- Property source: Mantis Action History fix rows and linked Issue `resolvedIn`.
- AI-agent actions: `derive_main_branchpoint`, `derive_merge_state`.

### Entity: `BranchRequest`
- Properties:
  - `requestId`, `requestStatus`, `requestedAt`
  - `branchName`, `majorVersion`, `minorVersion`, `branchPoint`, `branchType`, `forCustomer`
  - `top3CaseId`, `productFamily`, `requestorId`, `devLeadId`, `pmLeadId`
  - `branchSourceCodeUrl`, `branchJobUrl`
- Outgoing relations: `generatesBranch -> Branch`, `raisedForTop3Case -> Top3Case`, `requestedBy/hasDevLead/hasPmLead -> Person`
- Incoming relations: `submits <- BranchRequestPage(adapter)`, `updatesStatus <- EmailNotify`
- Property source: `branch-request-email-notify.txt`, `branch-approved-email-notify.txt`.
- AI-agent actions: `ingest_request_notify`, `merge_approval_notify`, `sync_request_state`.

### Entity: `Branch`
- Properties: `branchName`, `productFamily`, `majorVersion`, `minorVersion`, `branchPoint`, `branchType`, `createdAt`, `createdByApp`, `createdByRef`, `lastSyncedAt`, `sourceBranchRequestId`
- Outgoing relations: `hasDevLead -> Person`, `hasPmLead -> Person`, `exposesBuildEntry -> BuildRequestPage(adapter)`
- Incoming relations: `generatesBranch <- BranchRequest`, `usesBranch <- Top3Case|BuildRequest`
- Property source: Jenkins branch project pages and branch-approved emails.
- AI-agent actions: `verify_branch_sync`, `refresh_branch_metadata`.

### Entity: `BuildRequest`
- Properties:
  - `buildRequestId`, `requestedAt`, `requestStatus`
  - `gitProject`, `gitRef`, `gitlabUrl`
  - `branchType`, `patchVersion`, `buildLabel`, `buildLabelOther`
  - `emailRecipients`, `buildDescription`, `selectedCombinations`
  - `runDbUpdate`, `appendModelName`, `trial`, `maturity`, `sa`, `saPatchVersion`
- Outgoing relations: `usesBranch -> Branch`, `spawns -> BuildRun`
- Incoming relations: `openedFrom <- BuildRequestPage(adapter)`, `submitsBuildRequest <- Person`
- Property source: `br_7-4_logging_rsso_acc_msgs_38kg_build [Jenkins].html` form parameters.
- AI-agent actions: `prepare_build_payload`, `submit_build`, `track_build_queue_state`.

### Entity: `BuildRun`
- Properties: `buildRunId`, `buildTag`, `result`, `triggeredAt`, `finishedAt`, `notifiedAt`, `consoleOutputUrl`, `imagesStoreAt`, `notificationRef`
- Outgoing relations: `publishesTop3Build -> Top3Build`, `publishesArtifact -> BuildArtifact`
- Incoming relations: `spawns <- BuildRequest`, `validates <- ValidationRecord`
- Property source:
  - Jenkins build history pages.
  - Jenkins build emails (`SUCCESS` and `FAILURE` `.msg`) for status and URLs.
- AI-agent actions: `ingest_build_notify`, `mark_result`, `attach_artifact_folder`.

### Entity: `BuildArtifact` (new in v4.0)
- Properties: `artifactId`, `buildRunId`, `top3BuildId`, `name`, `artifactType`, `size`, `lastModifiedAt`, `downloadUrl`, `viewUrl`, `checksumRef`
- Outgoing relations: `belongsToBuildRun -> BuildRun`, `belongsToTop3Build -> Top3Build`
- Incoming relations: `publishesArtifact <- Top3Build|BuildRun`
- Property source: `InfoSite-b8997.html` artifact listing (name/time/size/actions).
- AI-agent actions: `crawl_infosite_folder`, `index_artifacts`, `match_artifacts_to_buildrun`.

### Entity: `ValidationRecord`
- Properties: `validationId`, `environment`, `reproSteps`, `observedResult`, `expectedResult`, `validationStatus`, `evidenceLink`
- Outgoing relations: `validates -> BuildRun`, `validatesIssue -> Issue`, `trackedIn -> PhabricatorTask`
- Incoming relations: `summarizes <- ReleaseNote`
- Property source: Phabricator checklist/comments and QA evidence links.
- AI-agent actions: `collect_validation_evidence`, `summarize_validation_status`.

### Entity: `ReleaseNote`
- Properties: `releaseNoteId`, `content`, `authorId`, `createdAt`, `publishStatus`
- Outgoing relations: `summarizes -> ValidationRecord`
- Incoming relations: `publishesDeliverable <- Top3Case`
- Property source: Mantis RN fields and release bugnotes.
- AI-agent actions: `draft_release_note`, `publish_after_approval`.

### Entity: `PhabricatorTask`
- Properties: `phabTaskId`, `phabUrl`, `top3CaseId`, `title`, `status`, `assignedToId`, `updatedAt`, `latestCommentAt`, `latestCommentSummary`, `latestChecklistUrls`, `checklistSyncState`
- Outgoing relations: `linkedTo -> Top3Case`, `assignedTo -> Person`, `authoredBy -> Person`
- Incoming relations: `references <- BugNote`, `trackedIn <- ValidationRecord`
- Property source: `⚓ T28209 ... Airtel.html`.
- AI-agent actions: `sync_phab_status`, `extract_checklist`, `sync_checklist_back_to_case`.

### Entity: `EmailNotify`
- Properties: `emailNotifyId`, `receivedAt`, `sender`, `titleRaw`, `eventType`, `branchName`, `branchSourceCodeUrl`, `branchJobUrl`, `buildNumber`, `imagesStoreAt`, `consoleOutputUrl`, `ticketUrl`
- Outgoing relations: `updatesStatus -> BranchRequest`, `notifiesBuildRun -> BuildRun`, `referencesCase -> Top3Case`
- Property source: branch request/approval text emails and Jenkins build `.msg` notifications.
- AI-agent actions: `classify_event`, `route_event_to_core_entity`.

### Entity: `Person`
- Properties: `personId`, `name`, `roleType`, `email`
- Incoming relations: owner/lead/assignee/requestor links from all core entities.
- Property source: Mantis/Jenkins/Phabricator/Email account tokens.
- AI-agent actions: `normalize_identity`, `merge_aliases`.

### Entity: `Team`
- Properties: `teamId`, `teamType`, `channelRef`, `topic`
- Outgoing relations: `usesThread -> CommunicationThread`
- Incoming relations: `hasTeam <- Top3Case`
- Property source: Teams thread metadata and workflow convention (Core/All).

### Entity: `CommunicationThread`
- Properties: `threadId`, `platform`, `topic`, `createdAt`, `lastMessageAt`
- Incoming relations: `usesThread <- Team`
- Property source: `teams-chat-core-1229978.md`.
- AI-agent actions: `digest_thread_updates`, `extract_action_items`.

## 2.1 Core to Adapter Anchors
| Core Entity | Primary Adapter Objects |
| --- | --- |
| Top3Case | `Mantis.Top3CasePage`, `Outlook.MantisNotifyEmail`, `Teams.CoreThreadView` |
| NFR | `Mantis.NFRPage` |
| Issue | `Mantis.IssuePage` |
| BugNote | `Mantis.BugNoteSection` |
| ActionHistory / Top3Build / Patch | `Mantis.ActionHistorySection` |
| BranchRequest | `Jenkins.BranchRequestFormPage`, `Outlook.BranchRequestEmail`, `Outlook.BranchApprovedEmail` |
| Branch | `Jenkins.BranchProjectPage`, `Outlook.BranchApprovedEmail` |
| BuildRequest | `Jenkins.BuildWithParametersPage` |
| BuildRun | `Jenkins.BranchProjectPage`, `Outlook.BuildNotifyEmail` |
| BuildArtifact | `InfoSite.BuildFolderPage` |
| PhabricatorTask / ValidationRecord | `Phabricator.TaskPage` |
| CommunicationThread / Team | `Teams.CoreThreadView` |

## 3. Derivation / Composition Rules
| Target Field | Rule | Inputs | AI Agent Responsibility |
| --- | --- | --- | --- |
| `Top3Case.caseSummary` | summarize case progression and blockers | case ticket + related issue/NFR/branch/build/validation signals | compose and refresh summary |
| `Top3Case.currentPhase` | classify workflow phase | status, bugnote headlines, build/validation activity | derive phase for orchestration |
| `Top3Case.closedAt` | infer closure time | bugnote headline `---Release---` or `---Close---` | persist closure timestamp |
| `Top3Case.closureReason` | infer normalized closure rationale | final status + release notes + final bugnotes | compose closure reason |
| `NFR.solutionTags` | normalize comma-separated solution values | `NFR.solutionRaw` | split/trim/dedupe tags |
| `NFR.referredIssueIds` | extract issue references | NFR description + bugnotes | parse and resolve issue IDs |
| `NFR.referredNfrIds` | extract NFR references | NFR description + bugnotes | parse and resolve NFR IDs |
| `NFR.conclusion` | infer NFR-level conclusion | NFR ticket evidence | compose conclusion |
| `Issue.referredIssueIds` | extract linked issue IDs | issue bugnotes | parse and resolve IDs |
| `Issue.referredNfrIds` | extract linked NFR IDs | issue bugnotes | parse and resolve IDs |
| `Issue.conclusion` | infer issue-level conclusion | issue ticket + history + notes | compose conclusion |
| `Patch.mainBranchPointRaw` | token extraction from issue resolution text | `Issue.resolvedIn` via `Patch.fixesIssue` | derive candidate branchpoint token |
| `Patch.mainBranchPoint` | numeric parse | `Patch.mainBranchPointRaw` | parse integer or null |
| `Patch.mergeToMainState` | classify merge state | `Patch.mainBranchPointRaw`, `Patch.mainBranchPoint` | map merged/not merged/unknown |
| `EmailNotify.eventType` | classify notification type | raw email subject/body/sender | route event for downstream sync |
| `BuildArtifact.artifactType` | infer artifact category | filename patterns from InfoSite list | classify image/package/log/metadata |

## 4. Open Confirmations
| Item | Why Open | Proposed Resolution | Owner |
| --- | --- | --- | --- |
| Build failure `.msg` deep fields | `.msg` extraction is noisy without dedicated parser | keep current header/url extraction; add robust parser in implementation stage | Peter + HPO |
| `ValidationRecord` minimal field set | source pages provide varied QA note styles | finalize mandatory fields during interview/review step | Peter + HPO |

## 5. Decision Log
- [2026-02-26] Promoted to `v4.0` and split Layer-1 model into core and adapter docs per guidance v2.2.
- [2026-02-26] Kept all prior canonical entities from `v3.2`, completed pending entity coverage, and normalized missing build notification and artifact semantics.
- [2026-02-26] Added canonical `BuildArtifact` entity from InfoSite and Jenkins success-notify evidence.
