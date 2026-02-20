# Data Model - Top3 Case v0.4 (Ontology-Aligned) - agent-top3pm

## Modeling Principle
Use ontology-style entities, typed relations, and identity keys to keep TOP3 traceability consistent across `Mantis`, `Teams`, `Jenkins`, `Outlook`, `InfoSite`, `Phabricator`, and `GitLab`.

## Core Entities
### `Top3Case`
- `caseId` (string, primary key)
- `title` (string)
- `description` (text)
- `status` (enum)
- `severity` (enum)
- `createdAt` (datetime)
- `updatedAt` (datetime)
- `deliverableSummary` (text)

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
6. `Team usesThread CommunicationThread`
7. `Person(PM) submitsBranchRequest BranchRequest`
8. `BranchRequest targets Top3Case`
9. `BranchRequest resultsIn Branch`
10. `Branch hasDevLead Person(Dev)`
11. `Branch hasPmLead Person(PM)`
12. `Branch exposesBuildEntry BuildRequestPage`
13. `Person(PM|Dev) submitsBuildRequest BuildRequest`
14. `BuildRequest openedFrom BuildRequestPage`
15. `BuildRequest usesBranch Branch`
16. `BuildRequest spawns BuildRun`
17. `BuildRun publishesArtifact Artifact`
18. `ValidationRecord validates BuildRun`
19. `ValidationRecord validatesIssue Issue`
20. `ValidationRecord trackedIn PhabricatorTask`
21. `ReleaseNote summarizes ValidationRecord`
22. `Top3Case publishesDeliverable ReleaseNote`

## Cardinality
1. `Top3Case -> NFR`: `1..*`
2. `Top3Case -> Issue`: `0..*`
3. `Top3Case -> Team`: `2..2`
4. `Top3Case -> BranchRequest`: `1..*`
5. `BranchRequest -> Branch`: `0..1`
6. `Branch -> BuildRequestPage`: `1..1`
7. `Branch -> Person(DevLead)`: `1..1`
8. `Branch -> Person(PMLead)`: `1..1`
9. `Branch -> BuildRequest`: `0..*`
10. `BuildRequest -> BuildRun`: `0..*`
11. `BuildRun -> Artifact`: `1..*`
12. `BuildRun -> ValidationRecord`: `0..*`
13. `Top3Case -> ReleaseNote`: `0..*`

## Identity and Link Keys
1. `Top3Case.caseId` = TOP3 `Mantis bug_id`.
2. `NFR.nfrId` = NFR `Mantis bug_id`.
3. `Issue.issueId` = bug-fix `Mantis bug_id`.
4. `BranchRequest.NEW_BRANCH_NAME` is normalized to `Branch.branchName` when request succeeds.
5. `Branch.branchName` links `Mantis/Phab/Jenkins` execution context.
6. `BranchRequest.DEV_LEAD` / `BranchRequest.PM_LEAD` map to `Person.personId`.
7. `BuildRequestPage.pageUrl` is the navigable endpoint from branch page.
8. `BuildRun.buildId/buildTag` links `Jenkins -> Outlook -> InfoSite`.
9. `Artifact.artifactPath` is `InfoSite` retrieval key.
10. `ValidationRecord.evidenceLink` links checklist/log artifacts in `Phabricator`.
11. `Team.channelRef` and `CommunicationThread.threadId` identify Core/All chat channels.

## AI Agent Fields
- `riskScore` (0-100)
- `blockerFlags` (array)
- `missingLinks` (array)
- `nextBestActions` (array)
