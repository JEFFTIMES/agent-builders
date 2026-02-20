# Apps v0.1 - agent-top3pm

## Core Systems
1. `Mantis` - case/ticket tracking (Top3 + linked issues/NFR).
2. `Teams` - real-time cross-functional technical coordination.
3. `Jenkins` - branch request and CI build execution.
4. `GitLab` - code branches and source changes.
5. `InfoSite` - build artifact browsing/download.

## Supporting Systems (likely)
1. `FortiCare` - customer support case reference.
2. `Phabricator` - task discussion/trace links observed in assets.

## App Roles in Workflow
- System of record for case state: `Mantis`.
- System of record for build status: `Jenkins`.
- System of record for delivered artifacts: `InfoSite`.
- Coordination channel: `Teams`.
