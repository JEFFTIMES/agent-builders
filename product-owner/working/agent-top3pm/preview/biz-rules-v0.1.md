# Business Rules v0.1 - agent-top3pm

1. Top3 items must be tracked in `Mantis` with clear status and ownership.
2. Priority/severity drives escalation speed and communication frequency.
3. A customer-impacting request may require a dedicated branch/build before mainline readiness.
4. Build/test evidence must be traceable to a specific branch/build tag.
5. Linked records (Top3 ticket, NFR, branch/build, collaboration thread) must stay synchronized.
6. PM is accountable for cross-team alignment and status transparency.
7. For customer PoC scenarios, "works in lab" is necessary but not sufficient without customer-context validation.
8. Status transition should reflect evidence (implementation, build result, validation output).

## AI Agent Actions Supporting Rules
- Auto-check rule compliance before status transition suggestions.
- Alert PM when evidence links are missing (no build tag, no validation note, no owner).
