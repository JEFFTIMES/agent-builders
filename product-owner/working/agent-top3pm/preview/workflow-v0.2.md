# Workflow v0.2 - agent-top3pm

## Scope

TOP3 project manager execution workflow for FortiOS critical tickets, based on HPO-confirmed process.

## Confirmed End-to-End Workflow

1. PM checks TOP3-category tickets in `Mantis`, selects newly assigned TOP3 ticket, and enters the case (example: `1229978`).
2. PM reviews linked/related `Mantis` items in ticket content, including NFR tickets (example: `1225001`) and bug-fix tickets (example: `1241381`).
3. PM identifies staffing from `Mantis` fields:

- Dev/Dev lead from `Assigned To`.
- QA/QA lead from `QA Assignee`.
- Customer-facing supporters from `Support/Field Contact`.

4. PM establishes two-tier virtual team in `Teams`:

- Core: Dev + QA only.
- All: Core + customer-facing participants.
- PM creates two Teams group chats to separate technical execution from broader customer-facing sync.

5. PM runs iterative delivery cycle until final images are delivered:

- Investigation -> issue reproduction -> software development -> image build -> QA validation.

6. During each cycle, PM:

- Request supplementary data, suc as topolgy, config, traffic sniff, debug logs, from the support team. 
- Organizes and facilitates discussion with Core and All groups, to collect data, diagnose the issues, design features.
- Uses `Jenkins` to create branch.
- Triggers image build in `Jenkins`.
- Checks build success/failure notifications in `Outlook`.
- Locates built images in `InfoSite`.
- Tracks QA progress in `Phabricator`.
- Updates progress and deliverables in TOP3 `Mantis` ticket.

7. QA responsibilities in each cycle:

- Requests additional data through 
- Reproduces issues and track status in `Phabricator`.
- Executes on-site diagnosis with customer, if needed.
- Validates interim and final builds.
- Writes release note.

8. Dev responsibilities in each cycle:

- Explain root cause and document it in issue ticket.
- Implement feature patch or fix patch.
- Merge code.

## Role Positioning by System

- `Mantis`: official TOP3 case record, dependencies, status, deliverables.
- `Teams`: two-tier communication and meeting execution.
- `Jenkins`: branch creation and build execution.
- `Outlook`: build notification channel.
- `InfoSite`: build artifact retrieval.
- `Phabricator`: QA tracking and validation-progress workspace.

## AI-Agent-In-The-Loop Actions

1. Detect newly assigned TOP3 tickets in `Mantis` and auto-build dependency map.
2. Draft two-tier participant roster from case fields and suggest Core/All chat setup checklist.
3. Monitor cycle state (Investigation/Repro/Dev/Build/QA) and detect stalled stages.
4. Correlate `Jenkins` build IDs, `Outlook` notifications, and `InfoSite` artifacts.
5. Summarize QA progress from `Phabricator` and prepare `Mantis` progress-update draft for PM approval.
6. Enforce evidence gating before PM marks milestone complete.