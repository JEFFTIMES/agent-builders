# Cross-App Managed Object Data Handoff v0.1 - agent-top3pm

## Managed Objects Passed Across Apps
1. `Case ID` (e.g., Mantis `1229978`).
2. `Related Case IDs` (e.g., parent NFR `1225001`, validation bugs).
3. `Branch Name` (e.g., `br_7-4_logging_rsso_acc_msgs`).
4. `Build Tag/Number` (e.g., `8997`, `8923`).
5. `Artifact Link` (InfoSite path).
6. `Validation Evidence` (traffic log snippets, repro command/output).
7. `Owner/Assignee` and status.

## Handoffs
1. Mantis -> Teams: case context, objective, owners.
2. Teams -> Jenkins/GitLab: branch/build request parameters.
3. Jenkins -> InfoSite: produced build artifacts and metadata.
4. Lab/Test -> Mantis: observed behavior and verification notes.
5. Mantis -> external stakeholders: current status and ETA framing.

## AI Agent Actions
- Normalize IDs/links into one case graph.
- Detect broken handoffs (e.g., ticket references build tag but no artifact link).
- Generate "missing handoff fields" checklist per case.
