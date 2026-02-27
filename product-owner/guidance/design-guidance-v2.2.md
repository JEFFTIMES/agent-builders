# Design Guidance v2.2

## 1. Purpose

Provide a practical modeling and documentation standard for agentic workflow design that keeps domain semantics stable while enabling application-level automation.

## 2. Scope

- Cross-application workflow modeling
- Data model + state/event/action modeling
- Human and agent interaction boundaries
- Artifact contracts and release quality gates

## 3. Unified Architecture: 4 Layers

### Layer 1: Domain Data Model

- Goal: define stable business entities and canonical relations.
- Content: entities, properties, cardinality, link keys, source traceability, canonical/snapshot rules.
- Rule: keep app-specific UI fields out of this layer.

### 2-subLayer Data Model Pattern in Layer 1

#### subLayer A: Canonical Core Layer

- Stable business model (`Top3Case`, `BranchRequest`, `Branch`, `Issue`, etc.)
- Canonical links and normalized keys
- System-independent semantics

#### subLayer B: Interface/Presentation Adapter Layer

- Application/page contracts (`Mantis.Top3CasePage`, `Jenkins.BranchRequestPage`, `Outlook.EmailNotify`)
- Field mappings: app field <-> canonical property
- Human-mimic interaction guidance for agent actions

#### Guardrails

1. Business meaning stays in Canonical Core Layer.
2. UI selectors/form fields/page behaviors stay in Interface Layer.
3. App-specific temporary fields do not enter canonical entities unless they are true business facts.
4. Each mapped field must specify source, owner, and write policy.

### Layer 2: State Model

- Goal: define workflow-critical lifecycle states on selected entities.
- Content: finite state enums, transitions, triggers, owners.
- Rule: define states only for workflow-gating, multi-step, operationally useful transitions.

### Layer 3: Interaction Model (Event-Action-DataObject)

- Goal: describe who did what, on which application object, when, and what event was emitted.
- Content: events, actions, actors, target objects, sequence flows, event semantics.
- Rule: events must represent meaningful object-level state transitions.

### Layer 4: Automation Policy Model

- Goal: define what the agent may read/write/execute and under what conditions.
- Content: touch policies (`agent_managed`, `agent_suggest_only`, `human_only`), trigger rules, exception handling, fallback actions.
- Rule: every external write path must have explicit policy and evidence requirement.

## 4. Layer Integration Rules

1. A Layer-3 event should map to a Layer-2 state transition on a concrete object.
2. A Layer-3 action that writes external data must reference Layer-4 policy.
3. Layer-1 canonical fields must not directly depend on UI-specific adapter fields.
4. Adapter-field changes must resolve through Layer-1 canonical mapping rules.
5. Version release requires consistency across all touched layers for the same workflow slice.

## 5. Artifact Roles

- `data-model-<scope>-vX.Y.md`: discussion-first source of truth for canonical entities, source checks, derivation rules, and decisions.
- `data-model-<scope>-graph-vX.Y.yaml`: canonical machine-readable graph.
- `data-model-<scope>-graph-vX.Y.mmd`: synchronized visualization view.
- `state-model-<scope>-vX.Y.md`: object-scoped state definitions and transition tables.
- `event-action-dataobj-flow.md`: scenario-level interaction flow drafts and refinements.
- `automation-policy-<scope>-vX.Y.md`: agent boundary and write-policy contract.

## 6. Input Artifacts (Required)

1. Source system artifacts
- `working/<product>/assets/**/*.html`
- `working/<product>/assets/**/*.msg`
- `working/<product>/assets/**/*.md`

2. Workflow and context artifacts
- `working/<product>/preview/workflow-*.md`
- `working/<product>/preview/apps-*.md`
- `working/<product>/preview/x-app-mo-data-handoff-*.md`

3. Existing model artifacts
- `working/<product>/preview/data-model-<scope>-v*.md`
- `working/<product>/preview/data-model-<scope>-graph-v*.yaml`
- `working/<product>/preview/data-model-<scope>-graph-v*.mmd`

4. Decision context
- HPO confirmations from conversation logs
- `working/<product>/sparks.md`
- `working/<product>/progress.md`

## 7. Output Artifacts (Required)

1. Canonical data model
- `working/<product>/preview/data-model-core-<scope>-vX.Y.md`
- `working/<product>/preview/data-model-adapter-<scope>-vX.Y.md`
- `working/<product>/preview/data-model-<scope>-graph-vX.Y.yaml`
- `working/<product>/preview/data-model-<scope>-graph-vX.Y.mmd`
- keep core/adapter models separated in .md format but combine them together in .mmd and .yaml format

2. State model
- `working/<product>/preview/state-model-<scope>-vX.Y.md`

3. Interaction model
- `working/<product>/preview/event-action-dataobj-flow.md`
- Optional: `working/<product>/preview/workflow-interaction-events-vX.Y.md`

4. Automation policy model
- `working/<product>/preview/automation-policy-<scope>-vX.Y.md`

5. Governance artifacts
- `working/<product>/progress.md`
- `working/<product>/sparks.md`

## 8. Discovery Method (Entity-Lifecycle-Centric)

Use a repeatable 4-lane pass for each workflow slice, then merge outputs into one versioned release.

### Lane A: Domain (Layer 1)

1. Select one canonical entity set for the current slice (e.g., `Top3Case + BranchRequest + EmailNotify`).
2. Update canonical properties/relations with source evidence and mapping rules.
3. Apply 2-subLayer separation:
- put stable business facts in Canonical Core.
- put app/page field contracts in Interface/Presentation Adapter.
4. Mark canonical vs snapshot fields explicitly.

### Lane B: State (Layer 2)

5. Define object-scoped states only when workflow-gating and multi-step.
6. Add finite state sets, transition rules, triggers, and owners.
7. Map key events to state transitions.

### Lane C: Interaction (Layer 3)

8. Capture event-action-dataobject flow for human/agent/system actions.
9. Record interaction evidence and emitted events (`event -> action -> object -> next event`).
10. Normalize repeated interactions into reusable event/action patterns.

### Lane D: Automation Policy (Layer 4)

11. Set read/write boundaries for each touched field/action (`agent_managed`, `agent_suggest_only`, `human_only`).
12. Define trigger/fallback/exception handling for external data touch points.

### Release Sync

13. Ask HPO to confirm unresolved mappings and policy exceptions.
14. Version up when definitions change; preserve previous versions.
15. Synchronize `.md`, `.yaml`, `.mmd` for the same version and semantics.

## 9. Minimal Documentation Contract per Version

Each modeling version must include:

1. Version metadata and source-of-truth pointers
2. Coverage dashboard (reviewed/pending)
3. Property source checks
4. Relation source checks
5. Derivation/composition rules
6. Open confirmations
7. Decision log

## 10. Standard Template

### 10.1 Data Model Template

```md
## Data Model Layer - <scope> - <Core/Adapter> v<version>

## 0. Metadata
- Product:
- Domain / Bounded Context:
- Discovery Scope Boundary:
- Entity Lifecycle Span (start -> end):
- Version:
- Last Updated (time, owner):
- Source Corpus Snapshot (systems/pages/files):
- Source-of-Truth Model File:
- Graph Model File (canonical YAML):
- Graph View File (Mermaid):

## 1. Coverage Dashboard
| Entity | Property Check | Relation Check | Status | Open Items |

## 2. Entity Discovery Cards (Canonical Core)
### Entity: <EntityName>
#### Properties
#### Outgoing/Incoming Relations
#### Property Source Check
#### Relation Source Check

## 2.1 Core/Adapter Mapping
| Adapter Object | App Field | Canonical Property | Read/Write Policy | Confirmation |

## 3. Derivation / Composition Rules
| Target Field | Rule | Inputs | Agent Responsibility | Confirmation |

## 3.1 State Model Snapshot
| Object | State Field | States | Transition Rule | Trigger |

## 3.2 Interaction Flow Snapshot
| Event | Actor | Action | Object | Emitted Event | Evidence |

## 3.3 Automation Policy Snapshot
| Field/Action | Policy | Tool Path | Human Approval Requirement |

## 4. Open Confirmations
| Item | Why Open | Proposed Resolution | Owner |

## 5. Decision Log
- [timestamp] Decision / rationale / changed files
```

### 10.2. States Model Template

```md
## State Model - <scope> v<version>

## 0. Metadata
- Product:
- Domain / Bounded Context:
- Scope:
- Version:
- Last Updated (time, owner):
- Source-of-Truth File:

## 1. State Inventory
| Object | State Field | State Set | Owner | Source / Derivation |
| --- | --- | --- | --- | --- |

## 2. Transition Table
| Object | From State | Trigger Event | Action | To State | Evidence | Confirmation |
| --- | --- | --- | --- | --- | --- | --- |

## 3. State Constraints
| Object | Rule | Type (hard/soft) | Exception Handling |
| --- | --- | --- | --- |

## 4. Open Confirmations
| Item | Why Open | Proposed Resolution | Owner |
| --- | --- | --- | --- |

## 5. Decision Log
- [timestamp] Decision / rationale / changed files
```

### 10.3. Interaction Model Template

```md
## Interaction Model - <scope> v<version>

## 0. Metadata
- Product:
- Scope:
- Version:
- Last Updated (time, owner):
- Source-of-Truth File:

## 1. Actors and Objects
### 1.1 Actors
| Actor | Type (human/agent/system) | Primary Applications | Notes |
| --- | --- | --- | --- |

### 1.2 Data Objects
| Data Object | Application | Canonical Mapping | Read/Write Policy |
| --- | --- | --- | --- |

## 2. Event-Action-Object Flow
| Event | Triggered By | Action | Object | Emits | Evidence |
| --- | --- | --- | --- | --- | --- |

## 3. Reusable Interaction Patterns
| Pattern Name | Preconditions | Steps | Outputs | Failure Path |
| --- | --- | --- | --- | --- |

## 4. Open Confirmations
| Item | Why Open | Proposed Resolution | Owner |
| --- | --- | --- | --- |

## 5. Decision Log
- [timestamp] Decision / rationale / changed files
```

### 10.4. Automation Policy Template

```md
## Automation Policy - <scope> v<version>

## 0. Metadata
- Product:
- Scope:
- Version:
- Last Updated (time, owner):
- Source-of-Truth File:

## 1. Field-Level Touch Policy
| Entity.Field or Adapter Field | Policy | Agent Capability | Human Approval | Evidence Required |
| --- | --- | --- | --- | --- |

## 2. Action-Level Policy
| Action | Trigger | Policy | Tool Path | Retry/Fallback | Escalation Rule |
| --- | --- | --- | --- | --- | --- |

## 3. Safety and Audit Rules
| Rule | Applies To | Enforcement | Audit Artifact |
| --- | --- | --- | --- |

## 4. Exception Register
| Exception | Rationale | Temporary/Permanent | Review Owner | Next Review Date |
| --- | --- | --- | --- | --- |

## 5. Open Confirmations
| Item | Why Open | Proposed Resolution | Owner |
| --- | --- | --- | --- |

## 6. Decision Log
- [timestamp] Decision / rationale / changed files
```

## 11. Quality Gate Before Version Release

1. No property without source or explicit derived/composed declaration.
2. No relation without source evidence and mapping rule.
3. Canonical vs adapter fields are clearly separated.
4. Events map to object-level state transitions.
5. Interaction steps identify actor/action/object/emitted-event.
6. External write permissions are explicit and policy-bound.
7. Open confirmations are listed explicitly.
8. `.md`, `.yaml`, and `.mmd` versions and semantics are synchronized.

## 12. References (Pattern Names)

Comparable industry patterns:
- Canonical Data Model + Anti-Corruption Layer
- Domain Model + View/DTO Model
- Page Object Model (RPA/testing style)
- System-of-Record + Integration Adapter
