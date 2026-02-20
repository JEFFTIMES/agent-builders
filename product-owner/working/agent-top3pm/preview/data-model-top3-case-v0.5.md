# Data Model - Top3 Case v0.5 (Ontology-Aligned) - agent-top3pm

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
- `caseSummary` (text)
- `customerName` (string, from Mantis Affected Customer)
- `pmOwnerId` (personId)
- `qaOwnerId` (personId)
- `devOwnerId` (personId)
- `supportOwnerIds` (list[personId])
- `branch` (string, from Branch Merge List)
- `hardwareModels` (list[enum], from Hardware Model)
- `currentPhase` (enum: investigation/repro/dev/build/qa/release)
- `closedAt` (datetime, nullable)
- `closureReason` (enum, nullable)
- `lastActionAt` (datetime, nullable)
- `lastActionType` (string, nullable)
- `actionCount` (integer, default 0)

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
- `note` (text)
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
11. `Team usesThread CommunicationThread`
12. `Person(PM) submitsBranchRequest BranchRequest`
13. `BranchRequest targets Top3Case`
14. `BranchRequest resultsIn Branch`
15. `Branch hasDevLead Person(Dev)`
16. `Branch hasPmLead Person(PM)`
17. `Branch exposesBuildEntry BuildRequestPage`
18. `Person(PM|Dev) submitsBuildRequest BuildRequest`
19. `BuildRequest openedFrom BuildRequestPage`
20. `BuildRequest usesBranch Branch`
21. `BuildRequest spawns BuildRun`
22. `BuildRun publishesArtifact Artifact`
23. `ValidationRecord validates BuildRun`
24. `ValidationRecord validatesIssue Issue`
25. `ValidationRecord trackedIn PhabricatorTask`
26. `ReleaseNote summarizes ValidationRecord`
27. `Top3Case publishesDeliverable ReleaseNote`

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
10. `Top3Case -> BranchRequest`: `1..*`
11. `BranchRequest -> Branch`: `0..1`
12. `Branch -> BuildRequestPage`: `1..1`
13. `Branch -> Person(DevLead)`: `1..1`
14. `Branch -> Person(PMLead)`: `1..1`
15. `Branch -> BuildRequest`: `0..*`
16. `BuildRequest -> BuildRun`: `0..*`
17. `BuildRun -> Artifact`: `1..*`
18. `BuildRun -> ValidationRecord`: `0..*`
19. `Top3Case -> ReleaseNote`: `0..*`

## Identity and Link Keys
1. `Top3Case.caseId` = TOP3 `Mantis bug_id`.
2. `Top3Case.mantisUrl` is the canonical case record URL.
3. `Top3Case.pmOwnerId`/`qaOwnerId`/`devOwnerId`/`supportOwnerIds` are denormalized snapshots of current owners; owner relations are canonical for ontology.
4. `NFR.nfrId` = NFR `Mantis bug_id`.
5. `Issue.issueId` = bug-fix `Mantis bug_id`.
6. `BugNote.bugNoteId` maps to Mantis bugnote ID when available.
7. `ActionHistory.rawRef` stores source item ID (Mantis history row ID or external event ID).
8. `BranchRequest.NEW_BRANCH_NAME` is normalized to `Branch.branchName` when request succeeds.
9. `Branch.branchName` links `Mantis/Phab/Jenkins` execution context.
10. `BranchRequest.DEV_LEAD` / `BranchRequest.PM_LEAD` map to `Person.personId`.
11. `BuildRequestPage.pageUrl` is the navigable endpoint from branch page.
12. `BuildRun.buildId/buildTag` links `Jenkins -> Outlook -> InfoSite`.
13. `Artifact.artifactPath` is `InfoSite` retrieval key.
14. `ValidationRecord.evidenceLink` links checklist/log artifacts in `Phabricator`.
15. `Team.channelRef` and `CommunicationThread.threadId` identify Core/All chat channels.

## AI Agent Fields
- `riskScore` (0-100)
- `blockerFlags` (array)
- `missingLinks` (array)
- `nextBestActions` (array)
