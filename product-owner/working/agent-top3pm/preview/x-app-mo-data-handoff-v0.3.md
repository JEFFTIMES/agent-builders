# Cross-App Managed Object Data Handoff v0.3 - agent-top3pm

## Ontology Scope
This model defines managed objects as ontology entities and uses typed relations for cross-app handoff traceability.

## Entities and Properties
### Entity: `Top3Case`
- `caseId` (string)
- `title` (string)
- `description` (text)
- `status` (enum)
- `severity` (enum)
- `createdAt` (datetime)
- `updatedAt` (datetime)
- `eta` (datetime)
- `deliverableSummary` (text)

### Entity: `NFR`
- `nfrId` (string)
- `title` (string)
- `description` (text)
- `status` (enum)
- `product` (enum)

### Entity: `Issue`
- `issueId` (string)
- `title` (string)
- `description` (text)
- `bug Notes` (list[text])
- `status` (enum)
- `category` (enum)
- `severity` (enum)

### Entity: `Branch`
- `branchName` (string)
- `baseVersion` (string)
- `createdBy` (personId)
- `createdAt` (datetime)

### Entity: `Build`
- `buildId` (string)
- `buildTag` (string)
- `result` (enum: success/failure)
- `triggeredAt` (datetime)
- `notifiedAt` (datetime)
- `notificationRef` (string)

### Entity: `Artifact`
- `artifactId` (string)
- `artifactPath` (string)
- `artifactType` (enum)
- `publishedAt` (datetime)

### Entity: `ValidationRecord`
- `validationId` (string)
- `environment` (string)
- `reproSteps` (text)
- `observedResult` (text)
- `expectedResult` (text)
- `validationStatus` (enum)
- `evidenceLink` (string)

### Entity: `ReleaseNote`
- `releaseNoteId` (string)
- `content` (text)
- `author` (personId)
- `createdAt` (datetime)

### Entity: `Person`
- `personId` (string)
- `name` (string)
- `roleType` (enum: PM/Dev/QA/Support)
- `level` (enum: normal/senior/manager/director/vp/svp)
- `specialty` (enum)

### Entity: `Team`
- `teamId` (string)
- `teamType` (enum: Core/All)
- `channelRef` (string)

### Entity: `CommunicationThread`
- `threadId` (string)
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
9. `Build publishesArtifact Artifact`
10. `Build notifiesVia OutlookNotification` (represented by `Build.notificationRef`)
11. `ValidationRecord validates Build`
12. `ValidationRecord validatesIssue Issue`
13. `ValidationRecord trackedIn PhabricatorTask`
14. `ReleaseNote summarizes ValidationRecord`
15. `Top3Case publishesDeliverable ReleaseNote`
16. `Top3Case synchronizedTo MantisTicket`

## Cardinality Constraints
1. `Top3Case -> NFR`: `1..*`
2. `Top3Case -> Issue`: `0..*`
3. `Top3Case -> Team`: `2..2` (Core and All)
4. `Top3Case -> Branch`: `1..*`
5. `Branch -> Build`: `1..*`
6. `Build -> Artifact`: `1..*`
7. `Build -> ValidationRecord`: `0..*`
8. `Top3Case -> ReleaseNote`: `0..*`

## Identity and Link Keys
1. `Top3Case.caseId` links to `Mantis bug_id` (TOP3 ticket).
2. `NFR.nfrId` links to `Mantis bug_id` for NFR ticket.
3. `Issue.issueId` links to `Mantis bug_id` for bug/fix ticket.
4. `Branch.branchName` links `Mantis/Phab/Jenkins` execution context.
5. `Build.buildTag` or `Build.buildId` links `Jenkins -> Outlook -> InfoSite`.
6. `Artifact.artifactPath` links retrieval location in `InfoSite`.
7. `ValidationRecord.evidenceLink` links checklist/log evidence in `Phabricator`.
8. `Team.channelRef` / `CommunicationThread.threadId` links to Teams Core/All chats.

## Handoff Chain (Ontology View)
1. `Mantis` creates/updates `Top3Case` and references `NFR` + `Issue`.
2. `Top3Case` instantiates `Team(Core)` and `Team(All)` in `Teams`.
3. `Top3Case usesBranch Branch` via `Jenkins` branch creation.
4. `Branch producesBuild Build` in `Jenkins`.
5. `Build notifiesVia OutlookNotification` in `Outlook`.
6. `Build publishesArtifact Artifact` in `InfoSite`.
7. `QA` creates `ValidationRecord` and tracks it in `Phabricator`.
8. `ValidationRecord` and `ReleaseNote` are synchronized back to `Top3Case` in `Mantis`.

## Worked Instance: Case `1229978`
- `Top3Case(1229978)` hasNFR `NFR(1225001)`.
- `Top3Case(1229978)` hasIssue `Issue(1241381)`.
- `Top3Case(1229978)` hasIssue `Issue(1241309)`.
- `Top3Case(1229978)` usesBranch `Branch(br_7-4_logging_rsso_acc_msgs)`.
- `Branch(br_7-4_logging_rsso_acc_msgs)` producesBuild `Build(8923)`.
- `Branch(br_7-4_logging_rsso_acc_msgs)` producesBuild `Build(8997)`.
- `Build(8923)` publishesArtifact `Artifact(InfoSite/build_tag_8923)`.
- `Build(8997)` publishesArtifact `Artifact(InfoSite/build_tag_8997)`.
- `ValidationRecord(v1)` validates `Build(8923)` and trackedIn `Phabricator(T28209)`.
- `ValidationRecord(v3)` validates `Build(8997)` and trackedIn `Phabricator(T28209)`.
- `Top3Case(1229978)` synchronizedTo `Mantis(1229978)` with deliverable updates.
