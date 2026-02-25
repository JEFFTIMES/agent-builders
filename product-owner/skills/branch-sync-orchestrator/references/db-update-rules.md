# DB Update Rules

## Idempotency

- Upsert `Branch` by `branchName`.
- Upsert `BranchPresence` by (`branchName`, `app`, `objectRef`, `fieldName`).
- Upsert `BranchSyncJob` by (`caseId`, `branchName`).

## Transition safety

- Use compare-and-set when moving `BranchSyncJob.state`.
- Reject illegal jumps (e.g., `requested -> mantis_updated` without evidence).
- Always append `BranchSyncTransitionLog` row.

## Transaction boundary

Per transition, do in one transaction:

1. Persist/refresh relevant `BranchPresence`.
2. Update `BranchSyncJob` state and metadata.
3. Insert transition log.

## Retry behavior

- Increment `attemptCount` on failure.
- Set `blocked` with `lastError` and `nextRetryAt`.
- Retry task must re-verify external sources before state changes.

## Concurrency

- Acquire job lock (`lockedBy`, `lockedAt`) before transition.
- Use lock timeout to recover abandoned jobs.
