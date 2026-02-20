# Cross-App Managed Object Data Handoff v0.2 - agent-top3pm

## Key Managed Objects
1. `Top3 Ticket ID` (e.g., `1229978`)
2. `Linked NFR/Bug IDs` (e.g., `1225001`, `1241381`)
3. `Role Assignments` (`Assigned To`, `QA Assignee`, `Support/Field Contact`)
4. `Team Chat IDs` (Core and All Teams groups)
5. `Branch Name` and `Build ID/Tag`
6. `Build Notification` (Outlook)
7. `Artifact Location` (InfoSite path)
8. `QA Validation State` (Phab)
9. `Release Notes` and `Deliverable summary`

## Confirmed Handoffs
1. `Mantis` -> `Teams`: case scope, owners, and coordination group setup.
2. `Mantis` -> `Jenkins`: branch/build intent and branch naming context.
3. `Jenkins` -> `Outlook`: build result notifications.
4. `Jenkins` -> `InfoSite`: artifact publication.
5. `InfoSite` -> QA/PM: testable image delivery.
6. QA execution -> `Phabricator`: reproduction and validation progress.
7. `Phabricator` + QA evidence -> `Mantis`: progress update and deliverable release notes.
