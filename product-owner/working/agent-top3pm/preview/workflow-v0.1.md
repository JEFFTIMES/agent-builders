# Workflow v0.1 - agent-top3pm

## Scope
Top3 case lifecycle for FortiOS critical feature/issues, centered on case `1229978` and linked tickets.

## As-Is End-to-End Workflow (cross-app)
1. Field/Sales/TAM opens or escalates request in `Mantis` (Top-3/NFR).
2. PM reviews severity/business impact and maps linked tickets/cases.
3. PM creates/alines collaboration thread in `Teams` with Dev/QA/Support.
4. Dev defines technical approach and required code branch strategy.
5. PM/Dev requests or confirms branch creation in `Jenkins` (`Request_Branch_GitLab`) and `GitLab`.
6. Dev implements and pushes changes; build jobs run in `Jenkins` matrix jobs.
7. Build artifacts are published and retrieved via `InfoSite`.
8. QA/PM validate behavior in lab and customer-like scenarios.
9. PM updates `Mantis` status, coordinates next actions, and communicates externally.

## Example Case Trace (RADIUS/RSSO)
- Requirement origin: `1225001` (NFR: richer RSSO/3GPP traffic log attributes).
- Top3 execution: `1229978` (Airtel PoC objective).
- Validation bugs on branch/build tags: `1241309`, `1241381`.
- Collaboration evidence: Teams thread with API/logging and branch coordination.

## AI-Agent-In-The-Loop (preview-level)
- AI agent ingests Mantis/Teams/Jenkins/InfoSite snapshots and builds a single case timeline.
- AI agent detects workflow blockers (missing branch, failing build, missing log field evidence).
- AI agent drafts status updates and next-action checklists for PM approval.
- AI agent flags cross-ticket dependency drift (e.g., NFR parent ticket vs. Top3 execution ticket).

## Open Items to Confirm with HPO
1. Official "entry point" app for new Top3 requests: only `Mantis`, or includes `FortiCare/Jira` intake?
2. Formal exit criteria for Top3 closure: PoC success, GA fix, customer acceptance, or all three?
3. Required handoff chain between PM, Dev, QA, TAM before status changes.
