# System Context - How Process Data Is Scattered Across Applications

Date: 2026-02-15
Step: 1 / Capture system context / Analyze how process data is scattered

## Scope
Analyzed artifacts under `working/agent-top3pm/assets/top3apps/` to map where PM-relevant process data is created, copied, transformed, and consumed.

## Key Finding
The same case context is fragmented into multiple system-specific records with different IDs and status models. PM acts as the human synchronizer across systems.

## Data Scatter Map

| Data object | Primary source | Also appears in | Scatter pattern | PM burden |
|---|---|---|---|---|
| Case identity (`Top3` ticket, e.g., `1229978`) | Mantis TOP3 ticket | Phabricator (`Mantis IDs`), Teams chat content | One case, many representations | Manual cross-referencing and context stitching |
| Requirement/problem context (NFR + related bugs, e.g., `1225001`) | Mantis (description, linked tickets, fields) | Teams discussion, Phabricator task narrative | Source details not centralized outside Mantis | PM repeatedly re-explains context across tools |
| Ownership/status (PM, Dev, QA assignments) | Mantis (`Assigned To`, `QA Assignee`, `Assigned PM`, `Status`) | Phabricator (`Assigned To`, status workflow), Teams mentions | Parallel status/ownership states with no strict sync | Risk of stale or conflicting status |
| Build branch and build execution state | Jenkins (`Request_Branch_GitLab`, branch jobs, build history) | Mantis note (`New branch created`), email notifications | Branch/build lifecycle lives outside ticket system | PM must backfill build outcomes into tracking tools |
| Build result notifications | Outlook/Jenkins email (`SUCCESS`/`FAILURE`, subject, console URL) | Jenkins pages (console/result history) | Alert channel separate from primary case tracker | PM manually triages alert -> case update actions |
| Build artifacts/images | InfoSite (`Special builds`, `Feature builds`, `builds/branches` by project) | Referenced from email/Jenkins context | Artifact repository detached from ticket and QA trackers | PM manually verifies and relays artifact location |
| QA workflow state | Phabricator (task status model: `investigating`, `reproduced`, `Build Failed`, `Built`, `done`) | Mantis status fields, Teams chat updates | Different status taxonomies per system | PM must translate and reconcile status semantics |
| Decision trail and technical discussion | Teams core chat (`teams-chat-core-1229978.md`) | Mantis bugnotes/comments, Phab comments | High-volume unstructured conversation | Hard to extract durable, reusable knowledge |

## Cross-Application Handoffs (As-Is)
1. `Mantis` intake and triage define the case anchor.
2. PM/engineers discuss and decide in `Teams`.
3. PM triggers branch/build operations in `Jenkins`.
4. Build outcomes arrive via `Outlook` notifications and `Jenkins` history.
5. Artifacts are confirmed in `InfoSite`.
6. QA execution/status is tracked in `Phabricator`.
7. PM writes outcome/progress back to `Mantis`.

## Where Data Is Most Scattered
1. **Status data**: Mantis + Phabricator + Jenkins + email all carry state signals but with different meanings.
2. **Traceability data**: ticket ID, task ID, branch name, build number, commit, artifact path are distributed across tools.
3. **Decision data**: decisions are made in Teams but formalized later in Mantis/Phab, causing lag and possible loss.

## Concrete Evidence Signals (from assets)
- Mantis includes PM execution fields: `Assigned PM`, `QA Assignee`, `Status`, `Summary`, `Affected Customer`, `Root Cause ECO/Mantis`, `Resolved In`.
- Mantis contains branch handoff notes (example: `New branch created: ...`).
- Jenkins shows branch/build lifecycle (`Build with Parameters`, build history with success/failed/aborted).
- Jenkins/Outlook notifications include result-bearing subjects and console URLs (`SUCCESS`/`FAILURE`, `X-Jenkins-Result`).
- InfoSite exposes build/branch/artifact navigation (`Special builds`, `Feature builds`, project-based links).
- Phabricator task includes Top3-specific workflow and Mantis linkage (`Mantis IDs`, status transitions like `investigating`, `reproduced`, `Build Failed`, `Built`, `done`).
- Teams core chat captures ongoing issue clarifications, confirmations, build-check interactions, and bugnote references.

## Interim Conclusion for Step 1
Current workflow requires PM to manually maintain cross-system consistency. The dominant structural issue is not missing data, but scattered ownership and duplicated state updates across tools.
