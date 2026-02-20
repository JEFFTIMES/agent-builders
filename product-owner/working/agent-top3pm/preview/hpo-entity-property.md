
### BRANCH Entity

Properties:
GITLAB_TOP_LEVEL_GROUP
`GITLAB_PROJECT` (enum)
`MAJOR_VERSION` (enum)
`MINOR_VERSION` (enum)
`NEW_BRANCH_NAME` (text)
`BRANCH_POINT` (enum:...)
`BRANCH_TYPE` (enum: R&D, Top3, NPI)
`BRANCH_DESCRIPTION` (text)
`FOR_CUSTOMER` (boolean)

Relationships:
`DEV_LEAD` (enum:...)
`PM_LEAD` (enum:...)


### Top3Case
`caseId` (string, primary key)
`title` (string) refers to Summary field of top3 case html
`caseCategory` (enum, ) refers to top3-case-category.md
`ETA` (datetime) refers to ETA field of top3 case html
`status` (enum: new, investigating, fix needed, solution sent, bumped out) refers to Status field of top3 case html
`severity` (enum: s1, s2, s3, s4) refers to Severity field of top3 case html
`createdAt` (datetime) refers to Date Submitted field of top3 case html
`updatedAt` (datetime) refers to Last Updte field of top3 case html
`mantisUrl` (string) refers to case URL
`description` (text) refers to Description field 
`deliverableSummary` (text)
`customerName` (string) refers to Affected Customer field
`pmOwnerId` (personId) 
`qaOwnerId` (personId)
`devOwnerId` (personId)
`supportOwnerIds` (list: personId,)
`branch` (string) refers to Branch Merge List field
`hardwareModels` (enum: FG-121G, FG-1801F,...) refers to Hardware Model field
`currentPhase` (enum: investigation/repro/dev/build/qa/release)
`closedAt` (datetime, nullable)
`closureReason` (enum, nullable)

### Top3Case Relations


## Typed Relations
1. `Top3Case hasNFR NFR`
2. `Top3Case hasIssue Issue`
3. `Top3Case ownedBy Person(PM)`
4. `Top3Case hasParticipant Person`
5. `Top3Case hasTeam Team`
6. `Top3Case hasBugNote BugNote`
7. `Top3Case hasActionHistory ActionHistory` 