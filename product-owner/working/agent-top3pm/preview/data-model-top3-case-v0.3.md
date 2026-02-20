# Data Model - Top3 Case v0.3 (Ontology-Aligned) - agent-top3pm

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

### `Branch`
- `branchName` (string, primary key, normalized from `NEW_BRANCH_NAME`)
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
- `BUILDING` (text:url to the building page)
- `createdAt` (datetime)

### `Build`
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
7. `Top3Case usesBranch Branch`
8. `Branch producesBuild Build`
9. `Branch hasDevLead Person(Dev)`
10. `Branch hasPmLead Person(PM)`
11. `Build publishesArtifact Artifact`
12. `ValidationRecord validates Build`
13. `ValidationRecord validatesIssue Issue`
14. `ValidationRecord trackedIn PhabricatorTask`
15. `ReleaseNote summarizes ValidationRecord`
16. `Top3Case publishesDeliverable ReleaseNote`

## Cardinality
1. `Top3Case -> NFR`: `1..*`
2. `Top3Case -> Issue`: `0..*`
3. `Top3Case -> Team`: `2..2`
4. `Top3Case -> Branch`: `1..*`
5. `Branch -> Build`: `1..*`
6. `Branch -> Person(DevLead)`: `1..1`
7. `Branch -> Person(PMLead)`: `1..1`
8. `Build -> Artifact`: `1..*`
9. `Build -> ValidationRecord`: `0..*`
10. `Top3Case -> ReleaseNote`: `0..*`

## Identity and Link Keys
1. `Top3Case.caseId` = TOP3 `Mantis bug_id`.
2. `NFR.nfrId` = NFR `Mantis bug_id`.
3. `Issue.issueId` = bug-fix `Mantis bug_id`.
4. `Branch.branchName` links `Mantis/Phab/Jenkins` execution context.
5. `Branch.DEV_LEAD` / `Branch.PM_LEAD` map to `Person.personId` in role relations.
6. `Build.buildId/buildTag` links `Jenkins -> Outlook -> InfoSite`.
7. `Artifact.artifactPath` is `InfoSite` retrieval key.
8. `ValidationRecord.evidenceLink` links checklist/log artifacts in `Phabricator`.
9. `Team.channelRef` and `CommunicationThread.threadId` identify Core/All chat channels.

## AI Agent Fields
- `riskScore` (0-100)
- `blockerFlags` (array)
- `missingLinks` (array)
- `nextBestActions` (array)
