# Step 1 - Problem Framing and Selection

Date: 2026-02-15
Basis: `drafts/system-context-data-scatter.md` + HPO succinct workflow

## Top 4 Identified Problems

1. Cross-system context fragmentation
- Case context is split across Mantis, Teams, Jenkins, InfoSite, Outlook notifications, and Phabricator.
- PM repeatedly reconstructs the same context for different participants and actions.
- Example:
  For TOP3 ticket `1229978`, PM reads requirement context in Mantis, discusses technical details in Teams (`teams-chat-core-1229978.md`), triggers branch/build in Jenkins, verifies artifacts in InfoSite, and checks QA state in Phabricator (`T28209`). No single screen shows the full case context end to end.

2. Traceability gap from intent to deliverable
- Links among ticket IDs, branch names, build numbers, commit references, QA tasks, and artifact locations are not unified.
- Auditing "why this build/artifact solves this ticket" is manual and error-prone.
- Example:
  To verify that a delivered image addresses `1229978`, PM must manually chain `Mantis ticket -> Jenkins branch/build -> commit link -> InfoSite image path -> Phabricator QA record`. This trace is distributed, not auto-linked as one auditable path.

3. Knowledge loss in execution conversations
- High-value reasoning (repro assumptions, workaround validation, root-cause hypotheses) lives in chat/comments.
- Knowledge is not consistently normalized into reusable structure for future TOP3 cases.
- Example:
  In `teams-chat-core-1229978.md`, participants discuss customer confirmations, attribute decisions, and reproduction/build checks. Only parts of these decisions are later copied into Mantis bugnotes, so future PMs cannot reliably reuse the full reasoning trail without re-reading long chat history.

4. Manual coordination overhead in two-tier collaboration
- PM maintains separate communication loops (`Core` and `All`) while also updating execution systems.
- Critical updates must be selectively propagated to the right audience at the right time.
- Example:
  PM creates/manages core and broader stakeholder chats, translates technical progress into stakeholder-facing updates, and in parallel keeps Mantis/Phabricator/Jenkins aligned; this introduces repetitive coordination work and delay risk.

## Proposed 3 Problems To Tackle In This Product

1. Cross-system context fragmentation.
2. Traceability gap from intent to deliverable.
3. Knowledge loss in execution conversations.

## Why Problem #4 (Manual coordination overhead) Is Not In First 3
- It is important but can be reduced indirectly by solving context fragmentation and traceability first.
- First release should prioritize shared context, evidence linkage, and knowledge reuse as the foundation.
- Collaboration-automation depth can be expanded in Phase 2.

## Product-Fit Statement
The AI-agent-based product should become the operational context and state layer across existing tools, while continuously capturing reusable domain knowledge from real execution.
