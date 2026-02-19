# Use Cases

> One file can contain multiple use cases. Keep each use case concrete and evidence-based.

## Use Case ID: UC-<number>

### 1. Use Case Name
- <short verb-object name>

### 2. Goal / Business Outcome
- <what this use case achieves>

### 3. Trigger
- <what starts this flow>

### 4. Actors
- Primary: <role>
- Supporting: <roles>
- AI Agent: <agent role>

### 5. Preconditions
- <state that must already be true>

### 6. Systems Involved
| System | Artifact Type | Purpose in Flow | Access/Sensitivity |
|---|---|---|---|
| <e.g., Mantis> | <ticket/comment/attachment> | <why used> | <internal/confidential/etc.> |

### 7. Main Flow (As-Is)
| Step | Actor | System | Action | Input Artifact | Output Artifact | Decision/Rule |
|---|---|---|---|---|---|---|
| 1 | <PM> | <Mantis> | <open TOP3 ticket> | <ticket id> | <context notes> | <rule> |

### 8. Alternate / Exception Flows
- A1: <condition> -> <deviation steps>
- E1: <error case> -> <recovery action>

### 9. Pain Points Observed
- <pain + evidence from interview>

### 10. AI-Agent Opportunities (To-Be Hypothesis)
| Step | Agent Action | Autonomy Level | Human Approver Role | Expected Benefit | Risk/Guardrail |
|---|---|---|---|---|---|
| <step ref> | <what agent does> | <suggest / approval-required / autonomous> | <PM/QA/etc.> | <time/quality gain> | <what to monitor> |

### 11. Data & Traceability Requirements
- Required links: <ticket -> build -> image -> QA -> release note>
- Required evidence snapshots: <what must be recorded>

### 12. Success Signals (Use-Case Level)
- <metric name>: <definition + how measured>

### 13. Open Questions / Assumptions
- <unknowns to resolve in Step 2/5>

---
