# Data Model Discovery - top3-workflow v1.9 - agent-top3pm

- Product: `agent-top3pm`
- Domain / Bounded Context: `Top3 PM cross-system delivery workflow (Mantis/Teams/Jenkins/Outlook/InfoSite/Phabricator)`
- Discovery Scope Boundary: `Managed objects, relations, link keys, and agent derivation rules for TOP3 case orchestration`
- Entity Lifecycle Span (start -> end): `Top3Case intake -> branch/build -> QA validation -> release/closure`
- Version: `v1.9`
- Last Updated (time, owner): `2026-02-23`, `Peter (PD)`
- Source Corpus Snapshot (systems/pages/files): `working/agent-top3pm/assets/top3apps/*.html`, `working/agent-top3pm/assets/top3apps/*.msg`, `working/agent-top3pm/assets/top3apps/teams-chat-core-1229978.md`
- Source-of-Truth Model File: `working/agent-top3pm/preview/data-model-top3-workflow-v1.9.md`
- Graph Model File (canonical YAML): `working/agent-top3pm/preview/data-model-top3-workflow-graph-v1.9.yaml`
- Graph View File (Mermaid): `working/agent-top3pm/preview/data-model-top3-workflow-graph-v1.9.mmd`

## 1. Coverage Dashboard

| Entity              | Property Check | Relation Check | Status       | Open Items                            |
| ------------------- | -------------- | -------------- | ------------ | ------------------------------------- |
| Top3Case            | Done           | Done           | Reviewed     | None                                  |
| NFR                 | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| Issue               | Done           | Done           | Reviewed     | None                                  |
| BugNote             | Done           | Done           | Reviewed     | None                                  |
| ActionHistory       | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| BranchRequest       | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| Branch              | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| BuildRequestPage    | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| BuildRequest        | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| BuildRun            | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| Artifact            | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| PhabricatorTask     | Done           | Done           | Reviewed     | None                                  |
| ValidationRecord    | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| ReleaseNote         | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| Person              | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| Team                | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| CommunicationThread | Pending        | Pending        | Not Reviewed | Add property + relation source checks |

## 2. Entity Discovery Cards

### Entity: `Top3Case`

#### Properties

- `caseId` (string, primary key)
- `title` (string, from Mantis Summary)
- `caseCategory` (enum, refer to `top3-case-category.md`)
- `ETA` (datetime, from Mantis ETA)
- `status` (enum: `new`/`investigating`/`fix needed`/`solution sent`/`bumped out`)
- `severity` (enum: `s1`/`s2`/`s3`/`s4`)
- `createdAt` (datetime, from Mantis `Date Submitted`)
- `updatedAt` (datetime, from Mantis `Last Update`)
- `mantisUrl` (string)
- `description` (text, from Mantis `Description`)
- `caseSummary` (text, AI-Agent inferred from reviewing cross-app project context: critical issues, obstacles, turning points, key persons and contributions)
- `customerName` (string, from Mantis `Affected Customer`)
- `pmOwnerId` (personId)
- `qaOwnerId` (personId)
- `devOwnerId` (personId)
- `supportOwnerIds` (list[personId])
- `branch` (string, from `Branch Merge List`)
- `hardwareModels` (list[enum], from `Hardware Model`)
- `currentPhase` (enum: investigation/repro/dev/build/qa/release)
- `currentPhaseSource` (enum: manual/derived_from_bugnote)
- `currentPhaseEvidenceNoteId` (string, nullable)
- `closedAt` (datetime, nullable, inferred from BugNote headline `---Release---` or `---Close---` defined in `preview/bugnote-headlines.md`)
- `closureReason` (enum, nullable, AI-agent inferred/composed from final state and closing evidence)

#### Outgoing Relations

- `hasNFR -> NFR` (`1..*`)
- `hasIssue -> Issue` (`0..*`)
- `ownedBy -> Person(PM)` (`1..1`)
- `hasParticipant -> Person` (contextual)
- `hasTeam -> Team` (`2..2`)
- `hasQaOwner -> Person(QA)` (`0..1`)
- `hasDevOwner -> Person(Dev)` (`0..1`)
- `hasSupportOwner -> Person(Support)` (`0..*`)
- `hasBugNote -> BugNote` (`0..*`)
- `hasActionHistory -> ActionHistory` (`0..*`)
- `publishesDeliverable -> ReleaseNote` (`0..*`)

#### Incoming Relations

- `targets <- BranchRequest`
- `references <- BugNote`

#### Property Source Check (Double-Checked)

- `caseId` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`bug_id` in case URL/context). `CONFIRMED`
- `title` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Summary`). `CONFIRMED`
- `caseCategory` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Category`), enum list from `working/agent-top3pm/assets/top3-case-category.md`. `CONFIRMED`
- `ETA` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`ETA`). `CONFIRMED`
- `status` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Status`). `CONFIRMED`
- `severity` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Severity`). `CONFIRMED`
- `createdAt` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Date Submitted`). `CONFIRMED`
- `updatedAt` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Last Update`). `CONFIRMED`
- `mantisUrl` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (case URL pattern). `CONFIRMED`
- `description` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Description`). `CONFIRMED`
- `caseSummary` <- PM-authored summary field in target model; not a direct Mantis field. `HPO-CONFIRMED DESIGN FIELD`
- `customerName` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Affected Customer`). `CONFIRMED`
- `pmOwnerId` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Assigned PM`) as current workaround. `HPO-CONFIRMED WORKAROUND`
- `qaOwnerId` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`QA Assignee`). `CONFIRMED`
- `devOwnerId` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Assigned To` as Dev/Dev lead per workflow). `HPO-CONFIRMED INTERPRETATION`
- `supportOwnerIds` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Support/Field Contact`). `CONFIRMED`
- `branch` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Branch Merge List`). `CONFIRMED`
- `hardwareModels` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Hardware Model`). `CONFIRMED`
- `currentPhase` <- derived lifecycle state from workflow + bugnote/action signals; not direct Mantis field. `HPO-CONFIRMED DESIGN FIELD`
- `currentPhaseSource` <- model provenance field. `DESIGN FIELD`
- `currentPhaseEvidenceNoteId` <- model provenance field. `DESIGN FIELD`
- `closedAt` <- inferred from BugNote `createdAt` where `headlineRaw` is `---Release---` or `---Close---` per `working/agent-top3pm/preview/bugnote-headlines.md`. `HPO-CONFIRMED RULE`
- `closureReason` <- AI Agent infers and composes from final state + release/close bugnote evidence for normalized closure rationale. `HPO-CONFIRMED RULE`

#### Relation Source Check (Double-Checked)

- `hasNFR` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`NFR` field). `CONFIRMED`
- `hasIssue` <- Mantis-linked issue IDs in case content and process references from HPO workflow. `HPO-CONFIRMED INTERPRETATION`
- `ownedBy(PM)` <- workflow governance role; current value sourced from Mantis `Assigned PM` as workaround. `HPO-CONFIRMED WORKAROUND`
- `hasParticipant` <- HPO-defined participators and Teams collaboration pattern. `HPO-CONFIRMED INTERPRETATION`
- `hasTeam` <- HPO-defined Core/All two-tier Teams structure. `HPO-CONFIRMED INTERPRETATION`
- `hasQaOwner` <- `QA Assignee` in Mantis. `CONFIRMED`
- `hasDevOwner` <- `Assigned To` in Mantis plus HPO role interpretation. `HPO-CONFIRMED INTERPRETATION`
- `hasSupportOwner` <- `Support/Field Contact` in Mantis. `CONFIRMED`
- `hasBugNote` <- `Bug Notes` section in Mantis. `CONFIRMED`
- `hasActionHistory` <- `Action History` section in Mantis. `CONFIRMED`
- `publishesDeliverable` <- bug notes (`---Release---`) + workflow release step. `HPO-CONFIRMED INTERPRETATION`

### Entity: `NFR`

#### Properties

- `nfrId` (string, primary key)
- `title` (string)
- `description` (text)
- `status` (enum)

#### Incoming Relations

- `hasNFR <- Top3Case` (`1..*`)
- `references <- BugNote` (`0..*`)

### Entity: `Issue`

#### Properties

- `issueId` (string, primary key)
- `title` (string)
- `description` (text)
- `category` (string, from Mantis `Category`)
- `severity` (string, from Mantis `Severity`)
- `reproducibility` (string, from Mantis `Reproducibility`)
- `reporterId` (personId, from Mantis `Reporter`)
- `keyword` (string, nullable, from Mantis `Keyword`)
- `reproducedById` (personId, nullable, from Mantis `Reproduced By`)
- `reportedVersion` (string, nullable, from Mantis `Reported Version`)
- `earliestBuildNumber` (string, nullable, from Mantis `Earliest Build Number`)
- `referredIssueIds` (list[string], derived from issue-local bug note parsing)
- `duplicateIssueId` (string, nullable, from Mantis `Duplicate ID`)
- `assignedDevId` (personId, from Mantis `Assigned To`)
- `qaAssigneeId` (personId, nullable, from Mantis `QA Assignee`)
- `fixSchedule` (string, nullable)
- `resolvedIn` (string, nullable)
- `conclusion` (text, AI-agent inferred/composed after end-to-end issue ticket review)
- `status` (enum)

#### Outgoing Relations

- `linksTo -> Issue` (`0..*`, from `referredIssueIds`)
- `duplicateOf -> Issue` (`0..1`, from `duplicateIssueId`)
- `fixedBy -> Person(Dev)` (`0..1`)
- `hasQaAssignee -> Person(QA)` (`0..1`)
- `hasBugNote -> BugNote` (`0..*`)

#### Incoming Relations

- `hasIssue <- Top3Case` (`0..*`)
- `references <- BugNote` (`0..*`)
- `validatesIssue <- ValidationRecord`

#### Property Source Check (Double-Checked)

- `issueId` <- linked Mantis issue ticket ID (`bug_id`) from issue page URL/context in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `title` <- issue `Summary` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `description` <- issue `Description` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `category` <- issue `Category` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `severity` <- issue `Severity` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `reproducibility` <- issue `Reproducibility` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `reporterId` <- issue `Reporter` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`; normalize account token to `Person.personId`. `CONFIRMED`
- `keyword` <- issue `Keyword` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `reproducedById` <- issue `Reproduced By` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`; normalize account token to `Person.personId`. `CONFIRMED`
- `reportedVersion` <- issue `Reported Version` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `earliestBuildNumber` <- issue `Earliest Build Number` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `referredIssueIds` <- AI-agent extraction from linked issue bug notes (`Issue.hasBugNote -> BugNote.rawText`) using text patterns (`#id`, `mantis#id`, `bug_id=id`) in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`; parsed values are deduplicated issue IDs linked back to the current issue. `HPO-CONFIRMED RULE`
- `duplicateIssueId` <- issue `Duplicate ID` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `assignedDevId` <- issue `Assigned To` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`; normalize account token to `Person.personId`. `CONFIRMED`
- `qaAssigneeId` <- issue `QA Assignee` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`; normalize account token to `Person.personId`. `CONFIRMED`
- `fixSchedule` <- issue `Fix Schedule` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `resolvedIn` <- issue `Resolved In` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `conclusion` <- AI Agent infers and composes issue-level conclusion from whole issue ticket evidence (`Summary`, `Description`, `Bug Notes`, assignment/status progression, and resolution fields). `HPO-CONFIRMED RULE`
- `status` <- issue `Status` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`

#### Relation Source Check (Double-Checked)

- `linksTo -> Issue` <- resolve outgoing issue links from `Issue.referredIssueIds` extracted from linked `Issue.hasBugNote -> BugNote.rawText` patterns (`#id`, `mantis#id`, `bug_id=id`), constrained to Mantis issue ticket type by lookup. `HPO-CONFIRMED RULE`
- `duplicateOf -> Issue` <- map `Issue.duplicateIssueId` from Mantis `Duplicate ID` to target `Issue.issueId` when value exists. `CONFIRMED`
- `fixedBy -> Person(Dev)` <- map `Issue.assignedDevId` from Mantis `Assigned To` to `Person.personId` (company account id token). `CONFIRMED`
- `hasQaAssignee -> Person(QA)` <- map `Issue.qaAssigneeId` from Mantis `QA Assignee` to `Person.personId` (company account id token). `CONFIRMED`
- `hasBugNote -> BugNote` <- issue `Bug Notes` section entries in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`, keyed by parent issue `bug_id` with note IDs. `CONFIRMED`
- `hasIssue <- Top3Case` <- Top3 case page links to issue tickets (e.g., `1241381`, `1241309`) in `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html`, aligned with `Top3Case.hasIssue` source-check and worked-instance mapping in `working/agent-top3pm/preview/x-app-mo-data-handoff-v0.3.md`. `HPO-CONFIRMED INTERPRETATION`
- `references <- BugNote` <- inverse mapping of `BugNote references -> Issue` through `mentionedTicketIds` parsing (`#id`, `mantis#id`, `bug_id=id`) and Mantis ticket-type resolution, per BugNote relation source-check. `HPO-CONFIRMED INTERPRETATION`
- `validatesIssue <- ValidationRecord` <- validation model defines `ValidationRecord validatesIssue -> Issue` with QA tracking context in `working/agent-top3pm/preview/x-app-mo-data-handoff-v0.3.md` and current workflow model. `HPO-CONFIRMED INTERPRETATION`

### Entity: `BugNote`

#### Properties

- `bugNoteId` (string, primary key)
- `ticketId` (string, parent Mantis ticket id)
- `authorDisplay` (string)
- `headlineType` (enum: update/summary/release/close/other)
- `headlineRaw` (string, nullable)
- `rawNote` (text)
- `rawHtml` (text)
- `rawText` (text)
- `noteKind` (enum: content_update/reminder/reply/system)
- `mentionedMantisIds` (list[string], from `#<mantisId>` extraction)
- `mentionedTicketIds` (list[string], from `#<mantisId>`/`mantis#<mantisId>`/`bug_id=<mantisId>`)
- `phabTaskIds` (list[string], from Phabricator lookup by `Top3Case.caseId` + QA owner identity)
- `createdAt` (datetime)
- `extractAt` (datetime, agent scrape timestamp; on revisit compare with Mantis note `createdAt` to detect updates)
- `bugNoteEditUrl` (string, derived shortcut URL)
- `bugNoteDeleteUrl` (string, derived shortcut URL)

#### Outgoing Relations

- `references -> Top3Case` (`0..*`)
- `references -> NFR` (`0..*`)
- `references -> Issue` (`0..*`)
- `references -> PhabricatorTask` (`0..*`)
- `authoredBy -> Person` (`1..1`, when author mapping is parseable)

#### Incoming Relations

- `hasBugNote <- Top3Case` (`0..*`)
- `hasBugNote <- Issue` (`0..*`)

#### Property Source Check (Double-Checked)

- `bugNoteId` <- Mantis bugnote operation URLs (`bugnote_edit_page.php?bugnote_id=...`, `bugnote_delete.php?bugnote_id=...`). `CONFIRMED`
- `ticketId` <- Mantis ticket context (`bug_id` in page URL and reply links). `CONFIRMED`
- `authorDisplay` <- Bugnote header display (`Name (account)` and `data-user`). `CONFIRMED`
- `createdAt` <- Bugnote header timestamp shown beside author. `CONFIRMED`
- `rawHtml`/`rawText`/`headlineRaw` <- Bugnote content block (`<td class="bugnote-note-public"><tt>...</tt></td>`). `CONFIRMED`
- `headlineType` <- derived from standardized headline patterns (e.g., `--- Update ---`) using `preview/bugnote-headlines.md`. `HPO-CONFIRMED RULE`
- `mentionedTicketIds` <- parsed from bugnote content patterns (`#id`, `mantis#id`, `bug_id=id`). `CONFIRMED`
- `phabTaskIds` <- queried from Phabricator by `Top3Case.caseId` + QA identity, then linked back to case/notes. `HPO-CONFIRMED RULE`
- `extractAt` <- capture timestamp generated by scraper; compare against page note datetime on subsequent visits. `HPO-CONFIRMED RULE`
- `bugNoteEditUrl`/`bugNoteDeleteUrl` <- derived from Mantis bugnote operation links using `bugNoteId`. `CONFIRMED`

#### Relation Source Check (Double-Checked)

- `references -> Top3Case` <- Bugnote parent ticket context from `ticketId` (`bug_id` in Mantis page URL + bugnote operation links). `CONFIRMED`
- `references -> NFR` <- `mentionedTicketIds` parsed from bugnote content (`#id`, `mantis#id`, `bug_id=id`) and resolved to NFR records via Mantis ticket lookup. `HPO-CONFIRMED INTERPRETATION`
- `references -> Issue` <- `mentionedTicketIds` parsed from bugnote content (`#id`, `mantis#id`, `bug_id=id`) and resolved to bug-fix issue records via Mantis ticket lookup. `HPO-CONFIRMED INTERPRETATION`
- `references -> PhabricatorTask` <- `phabTaskIds` resolved by AI agent Phabricator query using `Top3Case.caseId` + QA identity, then linked back to notes. `HPO-CONFIRMED RULE`
- `authoredBy -> Person` <- bugnote header author identity (`Name (account)` / `data-user`) mapped to `Person.personId` (company account id). `CONFIRMED`
- `hasBugNote <- Top3Case` <- Mantis `Bug Notes` section under parent ticket, keyed by `ticketId` and bugnote IDs. `CONFIRMED`
- `hasBugNote <- Issue` <- Mantis issue ticket `Bug Notes` section under parent issue `bug_id`, keyed by bugnote IDs. `CONFIRMED`

### Entity: `ActionHistory`

#### Properties

- `actionId` (string, primary key)
- `caseId` (string)
- `actorId` (personId)
- `actionType` (string)
- `actionAt` (datetime)
- `note` (text)
- `sourceSystem` (enum: Mantis/Teams/Jenkins/Phabricator)
- `rawRef` (string)

#### Incoming Relations

- `hasActionHistory <- Top3Case` (`0..*`)

### Entity: `BranchRequest`

#### Properties

- `branchRequestId` (string, primary key)
- `GITLAB_TOP_LEVEL_GROUP` (enum)
- `GITLAB_PROJECT` (enum)
- `MAJOR_VERSION` (enum)
- `MINOR_VERSION` (enum)
- `NEW_BRANCH_NAME` (text)
- `BRANCH_POINT` (enum)
- `BRANCH_TYPE` (enum: `R&D`/`Top3`/`NPI`)
- `BRANCH_DESCRIPTION` (text)
- `FOR_CUSTOMER` (boolean)
- `DEV_LEAD` (enum)
- `PM_LEAD` (enum)
- `requestStatus` (enum: submitted/succeeded/failed/cancelled)
- `requestedAt` (datetime)

#### Outgoing Relations

- `targets -> Top3Case`
- `resultsIn -> Branch` (`0..1`)

#### Incoming Relations

- `submitsBranchRequest <- Person(PM)`

### Entity: `Branch`

#### Properties

- `branchName` (string, primary key, normalized from `NEW_BRANCH_NAME`)
- `createdAt` (datetime)
- `sourceBranchRequestId` (string)

#### Outgoing Relations

- `hasDevLead -> Person(Dev)` (`1..1`)
- `hasPmLead -> Person(PM)` (`1..1`)
- `exposesBuildEntry -> BuildRequestPage` (`1..1`)

#### Incoming Relations

- `resultsIn <- BranchRequest`
- `usesBranch <- BuildRequest`

### Entity: `BuildRequestPage`

#### Properties

- `buildRequestPageId` (string, primary key)
- `pageUrl` (string)
- `jobPath` (string)
- `buttonLabel` (string, default: `Build with Parameters`)

#### Incoming Relations

- `exposesBuildEntry <- Branch`
- `openedFrom <- BuildRequest`

### Entity: `BuildRequest`

#### Properties

- `buildRequestId` (string, primary key)
- `requestedAt` (datetime)
- `requestStatus` (enum: submitted/queued/started/failed/cancelled)
- `selectedCombinations` (list[text])
- `RUN_DB_UPDATE` (boolean)
- `APPEND_MODEL_NAME` (boolean)
- `TRIAL` (boolean)
- `MATURITY` (enum: `F`/`M`)
- `SA` (boolean)
- `SA_PATCH_VERSION` (text)

#### Outgoing Relations

- `openedFrom -> BuildRequestPage`
- `usesBranch -> Branch`
- `spawns -> BuildRun` (`0..*`)

#### Incoming Relations

- `submitsBuildRequest <- Person(PM|Dev)`

### Entity: `BuildRun`

#### Properties

- `buildId` (string, primary key)
- `buildTag` (string)
- `result` (enum: success/failure)
- `triggeredAt` (datetime)
- `notifiedAt` (datetime)
- `notificationRef` (string)

#### Outgoing Relations

- `publishesArtifact -> Artifact` (`1..*`)

#### Incoming Relations

- `spawns <- BuildRequest` (`0..*`)
- `validates <- ValidationRecord`

### Entity: `Artifact`

#### Properties

- `artifactId` (string, primary key)
- `artifactPath` (string)
- `artifactType` (enum)
- `publishedAt` (datetime)

#### Incoming Relations

- `publishesArtifact <- BuildRun` (`1..*`)

### Entity: `PhabricatorTask`

#### Properties

- `phabTaskId` (string, primary key, e.g. `T28209`)
- `phabUrl` (string)
- `top3CaseId` (string, FK to `Top3Case.caseId`)
- `title` (string)
- `status` (string, current snapshot)
- `assignedToId` (personId, nullable)
- `updatedAt` (datetime, latest visible task activity time)
- `lastCheckedAt` (datetime, agent poll time)
- `lastStatus` (string, nullable, previous snapshot for delta detection)
- `latestCommentAt` (datetime, nullable)
- `latestCommentBy` (personId, nullable)
- `latestCommentSummary` (text, nullable)
- `latestChecklistUrls` (list[string])
- `latestChecklistNames` (list[string])
- `checklistSyncedToTop3At` (datetime, nullable)
- `checklistSyncState` (enum: pending/synced/failed)
- `checklistSyncError` (text, nullable)

#### Outgoing Relations

- `linkedTo -> Top3Case` (`1..1`)
- `assignedTo -> Person` (`0..1`)
- `authoredBy -> Person` (`0..1`)

#### Incoming Relations

- `references <- BugNote` (`0..*`)
- `trackedIn <- ValidationRecord` (`0..*`)

#### Property Source Check (Double-Checked)

- `phabTaskId` <- direct from Phab task ID token (e.g., `T28209`). `CONFIRMED`
- `title` <- direct from Phab task title. `CONFIRMED`
- `status` <- direct from Phab current status. `CONFIRMED`
- `assignedToId` <- direct from Phab assignee account ID. `CONFIRMED`
- `phabUrl` <- composed by PM/Agent as canonical task URL. `CONFIRMED`
- `top3CaseId` <- composed by PM/Agent from Phab-to-Mantis ID mapping context. `CONFIRMED`
- `updatedAt` <- composed by PM/Agent from latest visible timeline event timestamp. `CONFIRMED`
- `lastCheckedAt` <- composed by PM/Agent at each scrape/check run. `CONFIRMED`
- `lastStatus` <- composed by PM/Agent from previous cached snapshot. `CONFIRMED`
- `latestCommentAt` <- composed by PM/Agent from latest comment event timestamp. `CONFIRMED`
- `latestCommentBy` <- composed by PM/Agent from latest comment author account. `CONFIRMED`
- `latestCommentSummary` <- composed by PM/Agent as summarized extraction from latest comment. `CONFIRMED`
- `latestChecklistUrls` <- composed by PM/Agent from parsed checklist attachment links. `CONFIRMED`
- `latestChecklistNames` <- composed by PM/Agent from parsed checklist attachment names. `CONFIRMED`
- `checklistSyncedToTop3At` <- composed by PM/Agent sync timestamp to Mantis Top3. `CONFIRMED`
- `checklistSyncState` <- composed by PM/Agent sync workflow state. `CONFIRMED`
- `checklistSyncError` <- composed by PM/Agent error text from failed sync. `CONFIRMED`

#### Relation Source Check (Double-Checked)

- `linkedTo Top3Case` <- PM/Agent mapping using Mantis ID context from Phab task. `CONFIRMED`
- `assignedTo Person` <- direct from Phab assignee field. `CONFIRMED`
- `authoredBy Person` <- direct from Phab task author field. `CONFIRMED`
- `references <- BugNote` <- parsed `phabTaskIds` extraction and cross-linking workflow. `CONFIRMED`

### Entity: `ValidationRecord`

#### Properties

- `validationId` (string, primary key)
- `environment` (string)
- `reproSteps` (text)
- `observedResult` (text)
- `expectedResult` (text)
- `validationStatus` (enum)
- `evidenceLink` (string)

#### Outgoing Relations

- `validates -> BuildRun`
- `validatesIssue -> Issue`
- `trackedIn -> PhabricatorTask`

#### Incoming Relations

- `summarizes <- ReleaseNote`

### Entity: `ReleaseNote`

#### Properties

- `releaseNoteId` (string, primary key)
- `content` (text)
- `author` (personId)
- `createdAt` (datetime)

#### Outgoing Relations

- `summarizes -> ValidationRecord`

#### Incoming Relations

- `publishesDeliverable <- Top3Case` (`0..*`)

### Entity: `Person`

#### Properties

- `personId` (string, primary key)
- `name` (string)
- `roleType` (enum: PM/Dev/QA/Support)

#### Incoming Relations

- `ownedBy <- Top3Case`
- `hasQaOwner <- Top3Case`
- `hasDevOwner <- Top3Case`
- `hasSupportOwner <- Top3Case`
- `hasDevLead <- Branch`
- `hasPmLead <- Branch`
- `submitsBranchRequest <- Person(PM)`
- `submitsBuildRequest <- Person(PM|Dev)`

### Entity: `Team`

#### Properties

- `teamId` (string, primary key)
- `teamType` (enum: Core/All)
- `channelRef` (string)

#### Outgoing Relations

- `usesThread -> CommunicationThread`

#### Incoming Relations

- `hasTeam <- Top3Case`

### Entity: `CommunicationThread`

#### Properties

- `threadId` (string, primary key)
- `platform` (enum: Teams)
- `topic` (string)
- `createdAt` (datetime)

#### Incoming Relations

- `usesThread <- Team`

### 2.1 Common Identity and Link Keys (Global)

1. `Top3Case.caseId` = TOP3 `Mantis bug_id`.
2. `Top3Case.mantisUrl` is canonical case URL.
3. Owner IDs on `Top3Case` (`pmOwnerId`/`qaOwnerId`/`devOwnerId`/`supportOwnerIds`) are denormalized snapshots; owner relations are canonical.
4. `NFR.nfrId` = NFR `Mantis bug_id`.
5. `Issue.issueId` = bug-fix `Mantis bug_id`.
6. `BugNote.bugNoteId` maps to Mantis bugnote ID when available.
7. `BugNote.mentionedMantisIds` stores all `#<mantisId>` mentions.
8. `BugNote.mentionedTicketIds` are parsed Mantis cross-record references.
9. `BugNote.phabTaskIds` are resolved from Phabricator search using `Top3Case.caseId` + QA identity context.
10. `BugNote.bugNoteEditUrl`/`bugNoteDeleteUrl` are agent shortcut URLs derived from ticket and bugnote ids.
11. `Person.personId` should use unique company account id (e.g., `sjeff`) when mapping `BugNote authoredBy Person`.
12. `PhabricatorTask.phabTaskId` is the canonical cross-system key (e.g., `T28209`).
13. `PhabricatorTask.top3CaseId` links to `Top3Case.caseId` via Phab `Mantis IDs` mapping.
14. `Top3Case.currentPhase` may be derived from `BugNote.headlineType` plus note content cues, with provenance in `currentPhaseSource/currentPhaseEvidenceNoteId`.
15. Release summary content is captured in `BugNote(headlineType=release)`; no standalone Top3Case release-summary property.
16. `Top3Case.closureReason` is AI-agent inferred/composed from final state and close/release bugnote evidence.
17. `ActionHistory.rawRef` stores source item ID.
18. `BranchRequest.NEW_BRANCH_NAME` normalizes to `Branch.branchName` when request succeeds.
19. `Branch.branchName` links Mantis/Phab/Jenkins contexts.
20. `BranchRequest.DEV_LEAD` / `BranchRequest.PM_LEAD` map to `Person.personId`.
21. `BuildRequestPage.pageUrl` is branch-page navigable endpoint.
22. `BuildRun.buildId/buildTag` links `Jenkins -> Outlook -> InfoSite`.
23. `Artifact.artifactPath` is InfoSite retrieval key.
24. `ValidationRecord.evidenceLink` links checklist/log evidence in Phabricator.
25. `Team.channelRef` and `CommunicationThread.threadId` identify Core/All channels.
26. `Issue.hasBugNote -> BugNote` is the canonical issue-note linkage keyed by issue `bug_id` and bugnote IDs.
27. `Issue.referredIssueIds` are agent-extracted issue IDs from linked issue bugnote text (`Issue.hasBugNote -> BugNote.rawText`) using `#id`, `mantis#id`, `bug_id=id`.
28. `Issue.linksTo -> Issue` resolves from `Issue.referredIssueIds` after ticket-type filtering.
29. `Issue.duplicateIssueId` maps from Mantis `Duplicate ID`; `Issue.duplicateOf -> Issue` resolves when value is present.
30. `Issue.assignedDevId` maps from Mantis `Assigned To` and normalizes to `Person.personId` account token.
31. `Issue.qaAssigneeId` maps from Mantis `QA Assignee` and normalizes to `Person.personId` account token.
32. `Issue.fixSchedule` and `Issue.resolvedIn` map directly from same-name Mantis fields.
33. `Issue.category`, `Issue.severity`, `Issue.reproducibility`, `Issue.reporterId`, `Issue.keyword`, `Issue.reproducedById`, `Issue.reportedVersion`, and `Issue.earliestBuildNumber` map directly from same-name Mantis issue fields (`Reporter`/`Reproduced By` normalize to `Person.personId`).
34. `Issue.conclusion` is AI-agent inferred/composed from full issue-ticket evidence and stored as synthesized closure narrative.
35. `Issue.assignedDevId`/`Issue.qaAssigneeId` are denormalized snapshots; `Issue.fixedBy`/`Issue.hasQaAssignee` relations are canonical for graph traversal.
36. `PhabricatorTask.assignedToId` is denormalized snapshot; `PhabricatorTask.assignedTo` relation is canonical.

## 3. Derivation / Composition Rules

| Target Field                          | Rule                                                                   | Inputs                                                                                               | Agent Responsibility                                           | Confirmation                 |
| ------------------------------------- | ---------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- | ---------------------------- |
| `Top3Case.currentPhase`               | derive lifecycle phase from structured/unstructured case evidence      | `BugNote.headlineType`, note content cues                                                            | derive and persist phase state                                 | `HPO-CONFIRMED DESIGN FIELD` |
| `Top3Case.currentPhaseSource`         | provenance marker for phase derivation/manual override                 | latest phase update method                                                                           | persist provenance for auditability                            | `DESIGN FIELD`               |
| `Top3Case.currentPhaseEvidenceNoteId` | evidence pointer for current phase                                     | source `bugNoteId` used in derivation                                                                | record evidence pointer                                        | `DESIGN FIELD`               |
| `Top3Case.closedAt`                   | derive closure timestamp from close/release note                       | `BugNote.createdAt` where headline is `---Release---`/`---Close---`                                  | derive and set closure time                                    | `HPO-CONFIRMED RULE`         |
| `Top3Case.closureReason`              | infer and compose normalized closure rationale                         | final state + release/close bugnote evidence                                                         | infer and compose closure reason                               | `HPO-CONFIRMED RULE`         |
| `Issue.referredIssueIds`              | extract referenced issue IDs from linked issue bug notes               | `Issue.hasBugNote -> BugNote.rawText` (`#id`, `mantis#id`, `bug_id=id`)                              | parse, dedupe, and persist linked issue IDs                    | `HPO-CONFIRMED RULE`         |
| `Issue.conclusion`                    | infer and compose issue-level conclusion from full issue ticket review | `Issue.title`, `Issue.description`, `Issue.hasBugNote -> BugNote.rawText`, `Issue.status`, `Issue.resolvedIn`, assignment/repro metadata | synthesize concise conclusion for downstream PM/QA consumption | `HPO-CONFIRMED RULE`         |
| `riskScore`                           | compute risk score for case progression                                | blockers, missing links, status aging, severity                                                      | calculate and refresh score                                    | `DESIGN FIELD`               |
| `blockerFlags`                        | identify active blockers requiring escalation                          | dependency gaps, failed builds, stalled validation                                                   | detect and emit blocker flags                                  | `DESIGN FIELD`               |
| `missingLinks`                        | detect missing traceability links                                      | ticket/branch/build/artifact/validation mappings                                                     | compute missing link list                                      | `DESIGN FIELD`               |
| `nextBestActions`                     | recommend next executable actions                                      | current bottleneck + role ownership + SLA heuristics                                                 | generate ranked next actions                                   | `DESIGN FIELD`               |

## 4. Open Confirmations

| Item                                                  | Why Open                                         | Proposed Resolution                             | Owner       |
| ----------------------------------------------------- | ------------------------------------------------ | ----------------------------------------------- | ----------- |
| `NFR` property/relation source checks                 | source-check sections not yet documented in v1.9 | perform property + relation source-check review | Peter + HPO |
| `ActionHistory` property/relation source checks       | source-check sections not yet documented in v1.9 | perform property + relation source-check review | Peter + HPO |
| `BranchRequest` property/relation source checks       | source-check sections not yet documented in v1.9 | perform property + relation source-check review | Peter + HPO |
| `Branch` property/relation source checks              | source-check sections not yet documented in v1.9 | perform property + relation source-check review | Peter + HPO |
| `BuildRequestPage` property/relation source checks    | source-check sections not yet documented in v1.9 | perform property + relation source-check review | Peter + HPO |
| `BuildRequest` property/relation source checks        | source-check sections not yet documented in v1.9 | perform property + relation source-check review | Peter + HPO |
| `BuildRun` property/relation source checks            | source-check sections not yet documented in v1.9 | perform property + relation source-check review | Peter + HPO |
| `Artifact` property/relation source checks            | source-check sections not yet documented in v1.9 | perform property + relation source-check review | Peter + HPO |
| `ValidationRecord` property/relation source checks    | source-check sections not yet documented in v1.9 | perform property + relation source-check review | Peter + HPO |
| `ReleaseNote` property/relation source checks         | source-check sections not yet documented in v1.9 | perform property + relation source-check review | Peter + HPO |
| `Person` property/relation source checks              | source-check sections not yet documented in v1.9 | perform property + relation source-check review | Peter + HPO |
| `Team` property/relation source checks                | source-check sections not yet documented in v1.9 | perform property + relation source-check review | Peter + HPO |
| `CommunicationThread` property/relation source checks | source-check sections not yet documented in v1.9 | perform property + relation source-check review | Peter + HPO |

## 5. Decision Log

- [2026-02-23 10:07:03 PST] `Top3Case.closureReason` confirmed as AI-agent inferred/composed (`v1.2`).
- [2026-02-23 10:13:00 PST] `BugNote` relation source-check block completed (`v1.3`).
- [2026-02-23 10:52:52 PST] Discovery guidance updated to require explicit as-is and agent-oriented examples.
- [2026-02-23 10:52:52 PST] `v1.4` aligns data model documentation to lifecycle-centric discovery template.
- [2026-02-23 11:42:14 PST] `Issue` property/relation source-check completed and aligned to discovery template (`v1.5`).
- [2026-02-23 12:07:16 PST] Reopened `Issue` review and completed `v1.6` update: added `referredIssueIds`, `duplicateIssueId`, `assignedDevId`, `qaAssigneeId`, `fixSchedule`, `resolvedIn`, plus Issue->Issue/Person relations and derivation rule for issue bugnote reference extraction.
- [2026-02-23 13:06:34 PST] Reopened `Issue` review and completed `v1.7` update: added Mantis-mirrored fields (`category`, `severity`, `reproducibility`, `reporterId`, `keyword`, `reproducedById`, `reportedVersion`, `earliestBuildNumber`) and added agent-composed `Issue.conclusion` with derivation/source-check rules.
- [2026-02-23 13:17:09 PST] Reopened `Issue` review and completed `v1.8` normalization: standardized bug-note modeling to relation form by removing `Issue.bugNotes` and adding `Issue.hasBugNote -> BugNote`; updated derivation inputs to linked `BugNote.rawText`.
- [2026-02-23 13:24:48 PST] Cross-entity normalization audit (`v1.9`): fixed missing `Top3Case.devOwnerId` declaration in markdown entity card and clarified canonical rule that owner/assignee ID snapshot fields are denormalized while relations remain canonical.
