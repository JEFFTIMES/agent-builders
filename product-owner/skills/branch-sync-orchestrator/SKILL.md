---
name: branch-sync-orchestrator
description: Use this skill when implementing or operating branch propagation synchronization across Jenkins, email, Mantis, and InfoSite. Applies to full automation and pickup flows, maintains canonical Branch/BranchPresence/BranchSyncJob records, verifies propagation using link templates, and performs idempotent DB updates with retry-safe state transitions.
---

# Branch Sync Orchestrator

Use this skill when the task is to create, resume, verify, or repair branch propagation for Top3 workflow.

## Outcome

Maintain a single canonical branch view for the agent while synchronizing with external apps.

## Entities

- `Branch`: canonical branch identity (`branchName`, `createdByApp`, `createdByRef`)
- `BranchPresence`: where/how a branch appears in each app
- `BranchSyncJob`: orchestration state machine per case+branch

Read schema details from `references/db-model.md`.

## Modes

- `full_automation`: agent executes end-to-end propagation
- `pickup`: human started flow; agent detects current state and resumes missing steps

## State machine

`requested -> jenkins_created -> email_confirmed -> mantis_updated -> infosite_verified -> done`

Failure path: `blocked` with `lastError`, `nextRetryAt`.

## Required behavior

1. Always upsert entities idempotently using stable keys.
2. Never assume step completion from one source only; verify with source-specific checks.
3. Persist provenance on every update (`sourceSystem`, `sourceObjectRef`, `populationMode`, `capturedAt`).
4. On ambiguity (multiple candidate branches/cases), stop and request human confirmation.

## Workflow

1. Resolve or create `BranchSyncJob` by (`caseId`, `branchName`).
2. Load existing `BranchPresence` and infer current state.
3. Execute only missing transitions.
4. After each transition:
- verify externally
- upsert `BranchPresence`
- advance `BranchSyncJob.state`
5. If a step fails, set `blocked` and schedule retry.

## Verification tools and templates

Read `references/link-templates.md` for URL templates and check rules by system.

## DB update protocol

Read `references/db-update-rules.md` and apply:

- transaction per transition
- compare-and-set state advance
- monotonic timestamps
- append transition log rows

## Token extraction rule (ResolvedIn -> mainBranchPointRaw)

When patch info comes from `Issue.resolvedIn`, apply:

1. Normalize whitespace and separators.
2. Extract first numeric token (`\\d+`) as preferred branchpoint token.
3. If no numeric token exists, use the first normalized token.
4. Store token to `Patch.mainBranchPointRaw`.
5. Derive `Patch.mainBranchPoint` as integer only when token is numeric; else null.

## Pickup strategy

For pickup mode:

1. Discover evidence in this order: Jenkins -> Email -> Mantis -> InfoSite.
2. Build current-state snapshot from observed presences.
3. Advance only remaining steps.
4. Backfill missing `BranchPresence` rows for already-completed steps.

## Completion criteria

Mark `done` only when all are true:

- Jenkins creation confirmed
- email notification confirmed
- Mantis branch field confirmed
- InfoSite branch link/index confirmed
- DB entities and transition log are consistent
