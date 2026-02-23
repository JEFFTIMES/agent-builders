# Data Model - Top3 Workflow v1.0 (Entity-by-Entity, Discussion) - agent-top3pm

## Modeling Principle
Use ontology-style entities with per-entity properties and relations to support review discussions, while preserving the same graph semantics as v0.7.

## Entity: `Top3Case`
### Properties
- `caseId` (string, primary key)
- `title` (string, from Mantis Summary)
- `caseCategory` (enum, refer to `top3-case-category.md`)
- `ETA` (datetime, from Mantis ETA)
- `status` (enum: `new`/`investigating`/`fix needed`/`solution sent`/`bumped out`)
- `severity` (enum: `s1`/`s2`/`s3`/`s4`)
- `createdAt` (datetime, from Mantis Date Submitted)
- `updatedAt` (datetime, from Mantis Last Update)
- `mantisUrl` (string)
- `description` (text, from Mantis Description)
- `caseSummary` (text, PM-authored processing summary: critical issues, obstacles, turning points, key persons and contributions)
- `customerName` (string, from Mantis Affected Customer)
- `pmOwnerId` (personId)
- `qaOwnerId` (personId)
- `devOwnerId` (personId)
- `supportOwnerIds` (list[personId])
- `branch` (string, from Branch Merge List)
- `hardwareModels` (list[enum], from Hardware Model)
- `currentPhase` (enum: investigation/repro/dev/build/qa/release)
- `currentPhaseSource` (enum: manual/derived_from_bugnote)
- `currentPhaseEvidenceNoteId` (string, nullable)
- `closedAt` (datetime, nullable, inferred from BugNote headline `---Release---` or `---Close---` defined in `preview/bugnote-headlines.md`)
- `closureReason` (enum, nullable)

### Outgoing Relations
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

### Incoming Relations
- `targets <- BranchRequest`
- `references <- BugNote`

### Property Source Check (Double-Checked)
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
- `closureReason` <- inferred from final state/release/closure notes. `NEEDS-HPO-CONFIRMATION`

### Relation Source Check (Double-Checked)
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

## Entity: `NFR`
### Properties
- `nfrId` (string, primary key)
- `title` (string)
- `description` (text)
- `status` (enum)

### Incoming Relations
- `hasNFR <- Top3Case` (`1..*`)
- `references <- BugNote` (`0..*`)

## Entity: `Issue`
### Properties
- `issueId` (string, primary key)
- `title` (string)
- `description` (text)
- `bugNotes` (text)
- `status` (enum)

### Incoming Relations
- `hasIssue <- Top3Case` (`0..*`)
- `references <- BugNote` (`0..*`)
- `validatesIssue <- ValidationRecord`

## Entity: `BugNote`
### Properties
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

### Outgoing Relations
- `references -> Top3Case` (`0..*`)
- `references -> NFR` (`0..*`)
- `references -> Issue` (`0..*`)
- `references -> PhabricatorTask` (`0..*`)
- `authoredBy -> Person` (`1..1`, when author mapping is parseable)

### Incoming Relations
- `hasBugNote <- Top3Case` (`0..*`)

### Property Source Check (Double-Checked)
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

## Entity: `ActionHistory`
### Properties
- `actionId` (string, primary key)
- `caseId` (string)
- `actorId` (personId)
- `actionType` (string)
- `actionAt` (datetime)
- `note` (text)
- `sourceSystem` (enum: Mantis/Teams/Jenkins/Phabricator)
- `rawRef` (string)

### Incoming Relations
- `hasActionHistory <- Top3Case` (`0..*`)

## Entity: `BranchRequest`
### Properties
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

### Outgoing Relations
- `targets -> Top3Case`
- `resultsIn -> Branch` (`0..1`)

### Incoming Relations
- `submitsBranchRequest <- Person(PM)`

## Entity: `Branch`
### Properties
- `branchName` (string, primary key, normalized from `NEW_BRANCH_NAME`)
- `createdAt` (datetime)
- `sourceBranchRequestId` (string)

### Outgoing Relations
- `hasDevLead -> Person(Dev)` (`1..1`)
- `hasPmLead -> Person(PM)` (`1..1`)
- `exposesBuildEntry -> BuildRequestPage` (`1..1`)

### Incoming Relations
- `resultsIn <- BranchRequest`
- `usesBranch <- BuildRequest`

## Entity: `BuildRequestPage`
### Properties
- `buildRequestPageId` (string, primary key)
- `pageUrl` (string)
- `jobPath` (string)
- `buttonLabel` (string, default: `Build with Parameters`)

### Incoming Relations
- `exposesBuildEntry <- Branch`
- `openedFrom <- BuildRequest`

## Entity: `BuildRequest`
### Properties
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

### Outgoing Relations
- `openedFrom -> BuildRequestPage`
- `usesBranch -> Branch`
- `spawns -> BuildRun` (`0..*`)

### Incoming Relations
- `submitsBuildRequest <- Person(PM|Dev)`

## Entity: `BuildRun`
### Properties
- `buildId` (string, primary key)
- `buildTag` (string)
- `result` (enum: success/failure)
- `triggeredAt` (datetime)
- `notifiedAt` (datetime)
- `notificationRef` (string)

### Outgoing Relations
- `publishesArtifact -> Artifact` (`1..*`)

### Incoming Relations
- `spawns <- BuildRequest` (`0..*`)
- `validates <- ValidationRecord`

## Entity: `Artifact`
### Properties
- `artifactId` (string, primary key)
- `artifactPath` (string)
- `artifactType` (enum)
- `publishedAt` (datetime)

### Incoming Relations
- `publishesArtifact <- BuildRun` (`1..*`)

## Entity: `ValidationRecord`
### Properties
- `validationId` (string, primary key)
- `environment` (string)
- `reproSteps` (text)
- `observedResult` (text)
- `expectedResult` (text)
- `validationStatus` (enum)
- `evidenceLink` (string)

### Outgoing Relations
- `validates -> BuildRun`
- `validatesIssue -> Issue`
- `trackedIn -> PhabricatorTask`

### Incoming Relations
- `summarizes <- ReleaseNote`

## Entity: `ReleaseNote`
### Properties
- `releaseNoteId` (string, primary key)
- `content` (text)
- `author` (personId)
- `createdAt` (datetime)

### Outgoing Relations
- `summarizes -> ValidationRecord`

### Incoming Relations
- `publishesDeliverable <- Top3Case` (`0..*`)

## Entity: `Person`
### Properties
- `personId` (string, primary key)
- `name` (string)
- `roleType` (enum: PM/Dev/QA/Support)

### Incoming Relations
- `ownedBy <- Top3Case`
- `hasQaOwner <- Top3Case`
- `hasDevOwner <- Top3Case`
- `hasSupportOwner <- Top3Case`
- `hasDevLead <- Branch`
- `hasPmLead <- Branch`
- `submitsBranchRequest <- Person(PM)`
- `submitsBuildRequest <- Person(PM|Dev)`

## Entity: `Team`
### Properties
- `teamId` (string, primary key)
- `teamType` (enum: Core/All)
- `channelRef` (string)

### Outgoing Relations
- `usesThread -> CommunicationThread`

### Incoming Relations
- `hasTeam <- Top3Case`

## Entity: `CommunicationThread`
### Properties
- `threadId` (string, primary key)
- `platform` (enum: Teams)
- `topic` (string)
- `createdAt` (datetime)

### Incoming Relations
- `usesThread <- Team`

## Identity and Link Keys (Global)
1. `Top3Case.caseId` = TOP3 `Mantis bug_id`.
2. `Top3Case.mantisUrl` is canonical case URL.
3. Owner IDs on `Top3Case` are denormalized snapshots; owner relations are canonical.
4. `NFR.nfrId` = NFR `Mantis bug_id`.
5. `Issue.issueId` = bug-fix `Mantis bug_id`.
6. `BugNote.bugNoteId` maps to Mantis bugnote ID when available.
7. `BugNote.mentionedMantisIds` stores all `#<mantisId>` mentions.
8. `BugNote.mentionedTicketIds` are parsed Mantis cross-record references.
9. `BugNote.phabTaskIds` are resolved from Phabricator search using `Top3Case.caseId` + QA identity context.
10. `BugNote.bugNoteEditUrl`/`bugNoteDeleteUrl` are agent shortcut URLs derived from ticket and bugnote ids.
11. `Person.personId` should use unique company account id (e.g., `sjeff`) when mapping `BugNote authoredBy Person`.
12. `Top3Case.currentPhase` may be derived from `BugNote.headlineType` plus note content cues, with provenance in `currentPhaseSource/currentPhaseEvidenceNoteId`.
13. Release summary content is captured in `BugNote(headlineType=release)`; no standalone Top3Case release-summary property.
14. `ActionHistory.rawRef` stores source item ID.
15. `BranchRequest.NEW_BRANCH_NAME` normalizes to `Branch.branchName` when request succeeds.
16. `Branch.branchName` links Mantis/Phab/Jenkins contexts.
17. `BranchRequest.DEV_LEAD` / `BranchRequest.PM_LEAD` map to `Person.personId`.
18. `BuildRequestPage.pageUrl` is branch-page navigable endpoint.
19. `BuildRun.buildId/buildTag` links `Jenkins -> Outlook -> InfoSite`.
20. `Artifact.artifactPath` is InfoSite retrieval key.
21. `ValidationRecord.evidenceLink` links checklist/log evidence in Phabricator.
20. `Team.channelRef` and `CommunicationThread.threadId` identify Core/All channels.

## AI Agent Fields
- `riskScore` (0-100)
- `blockerFlags` (array)
- `missingLinks` (array)
- `nextBestActions` (array)
