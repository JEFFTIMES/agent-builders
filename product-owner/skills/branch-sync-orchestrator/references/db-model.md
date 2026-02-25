# DB Model (Branch Sync)

## Branch

- `branchName` (PK)
- `createdByApp` (`Jenkins`/`manual`/etc.)
- `createdByRef` (job/build/request ref)
- `createdAt`
- `updatedAt`

## BranchPresence

- `presenceId` (PK)
- `branchName` (FK -> Branch)
- `app` (`Jenkins`/`Email`/`Mantis`/`InfoSite`)
- `objectRef` (ticket id, job id, page id)
- `fieldName` (`NEW_BRANCH_NAME`, `Branch Merge List`, etc.)
- `accessLink` (nullable)
- `populationMode` (`agent_input`/`manual_copy`/`auto_sync`/`auto_detect`)
- `capturedAt`
- `rawValue` (nullable)

Unique recommendation: (`branchName`, `app`, `objectRef`, `fieldName`)

## BranchSyncJob

- `jobId` (PK)
- `caseId`
- `branchName`
- `mode` (`full_automation`/`pickup`)
- `state` (`requested`/`jenkins_created`/`email_confirmed`/`mantis_updated`/`infosite_verified`/`done`/`blocked`)
- `stateReason` (nullable)
- `attemptCount`
- `lastError` (nullable)
- `nextRetryAt` (nullable)
- `lockedBy` (nullable)
- `lockedAt` (nullable)
- `createdAt`
- `updatedAt`

Unique recommendation: (`caseId`, `branchName`)

## BranchSyncTransitionLog

- `transitionId` (PK)
- `jobId` (FK -> BranchSyncJob)
- `fromState`
- `toState`
- `action`
- `result` (`success`/`failed`)
- `error` (nullable)
- `createdAt`
