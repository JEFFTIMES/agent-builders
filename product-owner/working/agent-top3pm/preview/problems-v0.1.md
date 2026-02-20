# Problems v0.1 - agent-top3pm

## As-Is Constraints / Problems
1. Cross-app traceability is manual and fragile (Mantis/Teams/Jenkins/InfoSite links).
2. Parent-child dependency between NFR and Top3 execution can drift.
3. Validation signal is fragmented (commands/snippets in chat vs. formal ticket updates).
4. Branch/build metadata is easy to lose across handoffs.
5. Status can move faster than evidence consolidation.
6. Build failures may be noisy and require manual triage before actionable diagnosis.

## Impact
- Slower decision cycles.
- Higher risk of miscommunication to stakeholders/customers.
- PM overhead increases with case count.

## AI Opportunity
- Automated timeline synthesis, dependency checks, and evidence completeness checks before status updates.
