# Design Guidance v2.0

## 1. Purpose

Provide a practical modeling and documentation standard for agentic workflow design that keeps domain semantics stable while enabling application-level automation.

## 2. Scope

- Cross-application workflow modeling
- Data model + state/event/action modeling
- Human and agent interaction boundaries
- Artifact contracts and release quality gates

## 3. Unified Architecture: 4 Layers

### Layer 1: Core Domain Data Model

- Goal: define stable business entities and canonical relations.
- Content: entities, properties, cardinality, link keys, source traceability, canonical/snapshot rules.
- Rule: keep app-specific UI fields out of this layer.

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

## 4. Specific 2-Layer Data Model Pattern

### Layer A: Canonical Core Layer

- Stable business model (`Top3Case`, `BranchRequest`, `Branch`, `Issue`, etc.)
- Canonical links and normalized keys
- System-independent semantics

### Layer B: Interface/Presentation Adapter Layer

- Application/page contracts (`Mantis.Top3CasePage`, `Jenkins.BranchRequestPage`, `Outlook.EmailNotify`)
- Field mappings: app field <-> canonical property
- Human-mimic interaction guidance for agent actions

### Guardrails

1. Business meaning stays in Canonical Core Layer.
2. UI selectors/form fields/page behaviors stay in Interface Layer.
3. App-specific temporary fields do not enter canonical entities unless they are true business facts.
4. Each mapped field must specify source, owner, and write policy.

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
- `working/<product>/preview/data-model-<scope>-vX.Y.md`
- `working/<product>/preview/data-model-<scope>-graph-vX.Y.yaml`
- `working/<product>/preview/data-model-<scope>-graph-vX.Y.mmd`

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

1. Select one canonical entity at a time.
2. Capture/update properties and outgoing/incoming relations.
3. For each property, record source (`system/page/field/section`) or mark as derived/composed.
4. For each relation, record source evidence and mapping rule.
5. Separate canonical fields from interface-adapter fields.
6. Move agent-produced fields into `Derivation / Composition Rules`.
7. Add/update state definitions only when workflow-gating and multi-step.
8. Add/update interaction events/actions for data movement clarity.
9. Add/update automation policy and write boundaries for touched fields.
10. Ask HPO to confirm unresolved mappings.
11. Version up when entity definitions change; preserve older versions unchanged.
12. Sync `.md`, `.yaml`, and `.mmd` for the same version.

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

```md
# Data Model Discovery - <scope> v<version>

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

## 2.1 Interface/Presentation Adapter Mapping
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
