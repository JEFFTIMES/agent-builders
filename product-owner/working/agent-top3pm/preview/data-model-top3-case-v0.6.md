# Data Model - Top3 Case v0.6 (Ontology-Aligned) - agent-top3pm

## Modeling Principle

Use ontology-style entities, typed relations, and identity keys to keep TOP3 traceability consistent across `Mantis`, `Teams`, `Jenkins`, `Outlook`, `InfoSite`, `Phabricator`, and `GitLab`.

## Core Entities

### `Top3Case`

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
- `closedAt` (datetime, nullable)
- `closureReason` (enum, nullable)
- `lastActionAt` (datetime, nullable)
- `lastActionType` (string, nullable)
- `actionCount` (integer, default 0)

#### `Top3Case` Property Source Check (Double-Checked)

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
- `pmOwnerId` <- workflow ownership concept from HPO process (not explicit labeled Mantis field). `NEEDS-HPO-CONFIRMATION`
- `qaOwnerId` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`QA Assignee`). `CONFIRMED`
- `devOwnerId` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Assigned To` as Dev/Dev lead per workflow). `HPO-CONFIRMED INTERPRETATION`
- `supportOwnerIds` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Support/Field Contact`). `CONFIRMED`
- `branch` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Branch Merge List`). `CONFIRMED`
- `hardwareModels` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Hardware Model`). `CONFIRMED`
- `currentPhase` <- derived lifecycle state from workflow + bugnote/action signals; not direct Mantis field. `HPO-CONFIRMED DESIGN FIELD`
- `currentPhaseSource` <- model provenance field. `DESIGN FIELD`
- `currentPhaseEvidenceNoteId` <- model provenance field. `DESIGN FIELD`
- `closedAt` <- inferred from terminal status transition in Mantis action/history timeline. `NEEDS-HPO-CONFIRMATION`
- `closureReason` <- inferred from final state/release/closure notes. `NEEDS-HPO-CONFIRMATION`
- `lastActionAt` <- derived from latest Mantis `Action History` row timestamp. `CONFIRMED-DERIVED`
- `lastActionType` <- derived from latest Mantis `Action History` row event type. `CONFIRMED-DERIVED`
- `actionCount` <- derived count of `Action History` entries. `CONFIRMED-DERIVED`

### `NFR`

- `nfrId` (string, primary key)
- `title` (string)
- `description` (text)
- `status` (enum)

### `Issue`

- `issueId` (string, primary key)
- `title` (string)
- `description` (text)
- `bugNotes` (text)
- `status` (enum)

### `BugNote`

- `bugNoteId` (string, primary key)
- `caseId` (string)
- `authorId` (personId)
- `headlineType` (enum: update/summary/release/other)
- `headlineRaw` (string, nullable)
- `rawNote` (text)
- `mentionedMantisIds` (list[string], from `#<mantisId>` extraction)
- `mentionSnapshots` (list[object], extracted links with `{mantisId,resolvedEntityType,relationType}`)
- `phaseSignal` (enum: investigation/repro/dev/build/qa/release/unknown)
- `extractionVersion` (string)
- `extractionConfidence` (float: 0..1)
- `createdAt` (datetime)

### `ActionHistory`

- `actionId` (string, primary key)
- `caseId` (string)
- `actorId` (personId)
- `actionType` (string)
- `actionAt` (datetime)
- `note` (text)
- `sourceSystem` (enum: Mantis/Teams/Jenkins/Phabricator)
- `rawRef` (string)

### `BranchRequest`

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

### `Branch`

- `branchName` (string, primary key, normalized from `NEW_BRANCH_NAME`)
- `createdAt` (datetime)
- `sourceBranchRequestId` (string)

### `BuildRequestPage`

- `buildRequestPageId` (string, primary key)
- `pageUrl` (string)
- `jobPath` (string)
- `buttonLabel` (string, default: `Build with Parameters`)

### `BuildRequest`

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

### `BuildRun`

- `buildId` (string, primary key)
- `buildTag` (string)
- `result` (enum: success/failure)
- `triggeredAt` (datetime)
- `notifiedAt` (datetime)
- `notificationRef` (string)

### `Artifact`

- `artifactId` (string, primary key)
- `artifactPath` (string)
- `artifactType` (enum)
- `publishedAt` (datetime)

### `ValidationRecord`

- `validationId` (string, primary key)
- `environment` (string)
- `reproSteps` (text)
- `observedResult` (text)
- `expectedResult` (text)
- `validationStatus` (enum)
- `evidenceLink` (string)

### `ReleaseNote`

- `releaseNoteId` (string, primary key)
- `content` (text)
- `author` (personId)
- `createdAt` (datetime)

### `Person`

- `personId` (string, primary key)
- `name` (string)
- `roleType` (enum: PM/Dev/QA/Support)

### `Team`

- `teamId` (string, primary key)
- `teamType` (enum: Core/All)
- `channelRef` (string)

### `CommunicationThread`

- `threadId` (string, primary key)
- `platform` (enum: Teams)
- `topic` (string)
- `createdAt` (datetime)

## Typed Relations

1. `Top3Case hasNFR NFR`
2. `Top3Case hasIssue Issue`
3. `Top3Case ownedBy Person(PM)`
4. `Top3Case hasParticipant Person`
5. `Top3Case hasTeam Team`
6. `Top3Case hasQaOwner Person(QA)`
7. `Top3Case hasDevOwner Person(Dev)`
8. `Top3Case hasSupportOwner Person(Support)`
9. `Top3Case hasBugNote BugNote`
10. `Top3Case hasActionHistory ActionHistory`
11. `BugNote references Top3Case`
12. `BugNote references NFR`
13. `BugNote references Issue`
14. `Team usesThread CommunicationThread`
15. `Person(PM) submitsBranchRequest BranchRequest`
16. `BranchRequest targets Top3Case`
17. `BranchRequest resultsIn Branch`
18. `Branch hasDevLead Person(Dev)`
19. `Branch hasPmLead Person(PM)`
20. `Branch exposesBuildEntry BuildRequestPage`
21. `Person(PM|Dev) submitsBuildRequest BuildRequest`
22. `BuildRequest openedFrom BuildRequestPage`
23. `BuildRequest usesBranch Branch`
24. `BuildRequest spawns BuildRun`
25. `BuildRun publishesArtifact Artifact`
26. `ValidationRecord validates BuildRun`
27. `ValidationRecord validatesIssue Issue`
28. `ValidationRecord trackedIn PhabricatorTask`
29. `ReleaseNote summarizes ValidationRecord`
30. `Top3Case publishesDeliverable ReleaseNote`

### `Top3Case` Relation Source Check (Double-Checked)

- `Top3Case hasNFR NFR` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`NFR` field). `CONFIRMED`
- `Top3Case hasIssue Issue` <- Mantis-linked issue IDs in case content and process references from HPO workflow. `HPO-CONFIRMED INTERPRETATION`
- `Top3Case ownedBy Person(PM)` <- workflow governance role, not explicit Mantis field label. `NEEDS-HPO-CONFIRMATION`
- `Top3Case hasParticipant Person` <- HPO-defined participators (Dev/QA/Support/PM) and Teams collaboration pattern. `HPO-CONFIRMED INTERPRETATION`
- `Top3Case hasTeam Team` <- HPO-defined Core/All two-tier Teams structure, evidence in Teams artifact. `HPO-CONFIRMED INTERPRETATION`
- `Top3Case hasQaOwner Person(QA)` <- `QA Assignee` in Mantis. `CONFIRMED`
- `Top3Case hasDevOwner Person(Dev)` <- `Assigned To` in Mantis plus HPO role interpretation. `HPO-CONFIRMED INTERPRETATION`
- `Top3Case hasSupportOwner Person(Support)` <- `Support/Field Contact` in Mantis. `CONFIRMED`
- `Top3Case hasBugNote BugNote` <- `Bug Notes` section in Mantis. `CONFIRMED`
- `Top3Case hasActionHistory ActionHistory` <- `Action History` section in Mantis. `CONFIRMED`
- `Top3Case publishesDeliverable ReleaseNote` <- release communications in bug notes (`--- Release ---`) and workflow release step. `HPO-CONFIRMED INTERPRETATION`

## Cardinality

1. `Top3Case -> NFR`: `1..*`
2. `Top3Case -> Issue`: `0..*`
3. `Top3Case -> Team`: `2..2`
4. `Top3Case -> Person(PM owner)`: `1..1`
5. `Top3Case -> Person(QA owner)`: `0..1`
6. `Top3Case -> Person(Dev owner)`: `0..1`
7. `Top3Case -> Person(Support owner)`: `0..*`
8. `Top3Case -> BugNote`: `0..*`
9. `Top3Case -> ActionHistory`: `0..*`
10. `BugNote -> Top3Case(reference)`: `0..*`
11. `BugNote -> NFR(reference)`: `0..*`
12. `BugNote -> Issue(reference)`: `0..*`
13. `Top3Case -> BranchRequest`: `1..*`
14. `BranchRequest -> Branch`: `0..1`
15. `Branch -> BuildRequestPage`: `1..1`
16. `Branch -> Person(DevLead)`: `1..1`
17. `Branch -> Person(PMLead)`: `1..1`
18. `Branch -> BuildRequest`: `0..*`
19. `BuildRequest -> BuildRun`: `0..*`
20. `BuildRun -> Artifact`: `1..*`
21. `BuildRun -> ValidationRecord`: `0..*`
22. `Top3Case -> ReleaseNote`: `0..*`

## Identity and Link Keys

1. `Top3Case.caseId` = TOP3 `Mantis bug_id`.
2. `Top3Case.mantisUrl` is the canonical case record URL.
3. `Top3Case.pmOwnerId`/`qaOwnerId`/`devOwnerId`/`supportOwnerIds` are denormalized snapshots of current owners; owner relations are canonical for ontology.
4. `NFR.nfrId` = NFR `Mantis bug_id`.
5. `Issue.issueId` = bug-fix `Mantis bug_id`.
6. `BugNote.bugNoteId` maps to Mantis bugnote ID when available.
7. `BugNote.mentionedMantisIds` stores all `#<mantisId>` mentions extracted from note content.
8. `BugNote.mentionSnapshots` records resolved relation snapshots to `Top3Case`/`NFR`/`Issue`.
9. `Top3Case.currentPhase` may be derived from latest/high-confidence `BugNote.phaseSignal`; preserve provenance in `currentPhaseSource` and `currentPhaseEvidenceNoteId`.
10. Release summary content is captured in `BugNote(headlineType=release)`; no standalone release-summary property on `Top3Case`.
11. `ActionHistory.rawRef` stores source item ID (Mantis history row ID or external event ID).
12. `BranchRequest.NEW_BRANCH_NAME` is normalized to `Branch.branchName` when request succeeds.
13. `Branch.branchName` links `Mantis/Phab/Jenkins` execution context.
14. `BranchRequest.DEV_LEAD` / `BranchRequest.PM_LEAD` map to `Person.personId`.
15. `BuildRequestPage.pageUrl` is the navigable endpoint from branch page.
16. `BuildRun.buildId/buildTag` links `Jenkins -> Outlook -> InfoSite`.
17. `Artifact.artifactPath` is `InfoSite` retrieval key.
18. `ValidationRecord.evidenceLink` links checklist/log artifacts in `Phabricator`.
19. `Team.channelRef` and `CommunicationThread.threadId` identify Core/All chat channels.

## AI Agent Fields

- `riskScore` (0-100)
- `blockerFlags` (array)
- `missingLinks` (array)
- `nextBestActions` (array)