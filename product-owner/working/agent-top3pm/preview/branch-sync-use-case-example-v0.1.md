# Branch Synchronization Use Case Example (v0.1)

## Context

This use case describes how an AI agent maintains a single branch view across Jenkins, Email, Mantis, and InfoSite with a skill-guided, agentic approach.

## Purpose

- Keep a canonical branch entity while handling cross-app duplication.
- Let the agent synchronize and verify propagation in both full automation and pick-up modes.
- Persist evidence/provenance for auditability and recovery.

## Data Model Scope (for this use case)

- `Branch`: canonical branch identity.
- `Top3Build`: build grouping context.
- `Patch`: build-to-issue mapping.
- `Patch.mainBranchPointRaw`: extracted from `fixesIssue -> Issue.resolvedIn`.
- `Patch.issueTitle`: sourced from `fixesIssue -> Issue.title`.
- `Top3Case usesBranch -> Branch`: relation-first linkage.

## Case A: Full Automation

Goal: branch is created and propagation is verified end-to-end.

1. Agent creates branch in Jenkins.
2. Agent verifies notification evidence:
- Primary: check email notification.
- Fallback: if email does not arrive in expected time, agent proactively searches Jenkins history/metadata to confirm branch creation and continues with lower-confidence flag until subsequent cross-checks complete.
3. Agent updates/verifies Mantis `Branch Merge List`.
4. Agent verifies InfoSite branch presence/link.
5. Agent commits canonical state and evidence/provenance to memory storage.

## Case B: Pick-up (Partial Automation)

Goal: resume from human-started progress and complete missing propagation.

1. Agent scans Jenkins/Email/Mantis/InfoSite for existing branch evidence.
2. Agent infers current stage from observed evidence graph.
3. Agent executes only missing steps.
4. Agent backfills canonical links and provenance records.
5. Agent verifies consistency across all systems.

## Skill Role

The skill guides the agent with:

- observation rules
- action rules
- verification checks
- fallback strategies
- confidence/escalation policy
- token extraction rule (`Issue.resolvedIn -> Patch.mainBranchPointRaw`)

## Expected Output of This Use Case Pattern

- A completed branch propagation state (`done` or explicit `blocked` reason).
- A synchronized canonical branch view.
- Traceable evidence for each step and reconciliation decision.
