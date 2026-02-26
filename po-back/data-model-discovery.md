# Data Model Discovery Guidance

## Objective

- Keep data-model discovery continuous across the entity lifecycle.
- Produce artifacts that support both as-is traceability and agentic automation design.
- Align discovery execution to `guidance/design-guidance-v1.0.md`.

## Output Intent (Hybrid)

This guidance uses a hybrid intent:

1. As-is reflection
- Property/relation source checks tied to current systems.

2. Agent-oriented design
- Derived/composed fields, interaction/event/state models, and automation policy boundaries.

## Modeling Architecture (4 Layers)

### Layer 1: Core Domain Data Model

- Stable business entities and canonical relations.
- Includes source traceability, link keys, cardinality, and canonical/snapshot rules.

### Layer 2: State Model

- Workflow-critical lifecycle states attached to specific entities.
- Includes finite states, transitions, triggers, and owners.

### Layer 3: Interaction Model (Event-Action-DataObject)

- Captures actor/action/object/event sequences for human and agent operations.
- Events are meaningful state transitions.

### Layer 4: Automation Policy Model

- Defines what the agent can read/write/execute and under what constraints.
- Includes `external_data_touch_policy`, triggers, fallback, and exception rules.

## Data-Model Structure (2 Layers)

### A. Canonical Core Layer

- Stable business entities (`Top3Case`, `Issue`, `Branch`, `Build`, etc.).
- System-independent semantics.

### B. Interface/Presentation Adapter Layer

- App/page contracts (`Mantis.Top3CasePage`, `Jenkins.BranchRequestPage`, `Outlook.EmailNotify`, etc.).
- Field mappings between app interface fields and canonical properties.
- Human-mimic guidance for upcoming agent interactions.

### Guardrails

1. Keep business meaning in Canonical Core.
2. Keep UI selectors/page fields/interaction details in Interface Layer.
3. Do not promote temporary app-specific fields into canonical entities unless they are business facts.
4. Every mapped field must include source, owner, and write policy.

## Artifact Roles

- `data-model-<scope>-vX.Y.md`: discussion-first source of truth for canonical entities, source checks, derivation rules, and decisions.
- `data-model-<scope>-graph-vX.Y.yaml`: canonical machine-readable graph.
- `data-model-<scope>-graph-vX.Y.mmd`: visualization view synchronized with the same version.
- `state-model-<scope>-vX.Y.md`: object-scoped state definitions and transition tables.
- `event-action-dataobj-flow.md`: scenario-level interaction flow drafts and refinements.
- `automation-policy-<scope>-vX.Y.md`: agent boundary and write-policy contract.

## Required Inputs

1. Source system artifacts
- `working/<product>/assets/**/*.html`
- `working/<product>/assets/**/*.msg`
- `working/<product>/assets/**/*.md`

2. Workflow/context artifacts
- `working/<product>/preview/workflow-*.md`
- `working/<product>/preview/apps-*.md`
- `working/<product>/preview/x-app-mo-data-handoff-*.md`

3. Existing model artifacts
- `working/<product>/preview/data-model-<scope>-v*.md`
- `working/<product>/preview/data-model-<scope>-graph-v*.yaml`
- `working/<product>/preview/data-model-<scope>-graph-v*.mmd`

4. Decision context
- HPO confirmations from discussion logs
- `working/<product>/sparks.md`
- `working/<product>/progress.md`

## Required Outputs

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

5. Governance updates
- `working/<product>/progress.md`
- `working/<product>/sparks.md`

## Discovery Method (Entity-Lifecycle-Centric)

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

## Discovery Template

Use this structure in each checkpoint:

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

## Quality Gate Before Version Release

1. No property without source or explicit derived/composed declaration.
2. No relation without source evidence and mapping rule.
3. Canonical vs adapter fields are clearly separated.
4. Events map to object-level state transitions.
5. Interaction steps identify actor/action/object/emitted-event.
6. External write permissions are explicit and policy-bound.
7. Open confirmations are listed explicitly.
8. `.md`, `.yaml`, and `.mmd` versions and semantics are synchronized.
