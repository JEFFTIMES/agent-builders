# Data Model Discovery Guidance

## Objective

- Keep data-model discovery continuous across the full entity lifecycle, not confined to a single workflow step.
- Produce artifacts that are useful for both current-state traceability and future AI-agent implementation.

## Output Intent (Hybrid)

- It is not only a mirror of existing systems, and not only future design.
- It combines:

1. As-is reflection: explicit property/relation source checks tied to current systems.
2. Agent-oriented design: derived/composed fields and rules for future automation.

## Artifact Roles

- `data-model-<scope>-vX.Y.md`: discussion-first source with entity definitions and source checks.
- `data-model-<scope>-graph-vX.Y.yaml`: canonical graph model (machine-readable, versioned, handoff-ready).
- `data-model-<scope>-graph-vX.Y.mmd`: visualization view (derived view for review, not canonical).

## Discovery Method (Entity-Lifecycle-Centric)

1. Select one entity at a time.
2. Capture or update properties.
3. Capture or update outgoing/incoming relations.
4. For each property, record source (`system/page/field/section`) or mark as derived/composed design field.
5. For each relation, record relation source evidence and mapping rule.
6. Field placement rule: if a field is directly read from a system source, keep it in entity property/relation source-check sections.
7. Field placement rule: if a field is produced/maintained by agent logic, define it in `3. Derivation / Composition Rules` with rule, inputs, agent responsibility, and confirmation status.
8. Add or update derivation/composition rules with explicit AI-agent responsibility.
9. Ask HPO to confirm unresolved mappings.
10. Version up when entity definition is amended; preserve old versions unchanged.
11. Sync `.md`, `.yaml`, and `.mmd` files for the new version.

## Discovery Template

Use the following structure in each discovery report/checkpoint:

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

## 2. Entity Discovery Card: <EntityName>
### 2.1 Properties
| Property | Type | Source (system/page/field) | Agent Action (read/derive/compose) | Confirmation |

### 2.2 Relations
| Relation | Direction | Target | Source Evidence | Resolution Rule | Confirmation |

## 3. Derivation / Composition Rules
| Target Field | Rule | Inputs | Agent Responsibility | Confirmation |

## 4. Open Confirmations
| Item | Why Open | Proposed Resolution | Owner |

## 5. Decision Log
- [timestamp] Decision / rationale / changed files
```

## Simple Example

```md
## Example A: As-Is Reflection (source-checked)
## 2. Entity Discovery Card: Top3Case
### 2.1 Properties
| Property | Type | Source (system/page/field) | Agent Action | Confirmation |
|---|---|---|---|---|
| caseId | string | Mantis / Case page / bug_id | read | CONFIRMED |

### 2.2 Relations
| Relation | Direction | Target | Source Evidence | Resolution Rule | Confirmation |
|---|---|---|---|---|---|
| hasBugNote | incoming | BugNote | Mantis Bug Notes section | link by ticketId + bugNoteId | CONFIRMED |


## Example B: Agent-Oriented Design (derived/composed)
## 3. Derivation / Composition Rules
| Target Field | Rule | Inputs | Agent Responsibility | Confirmation |
|---|---|---|---|---|
| currentPhaseSource | provenance field for phase derivation | BugNote headline/content cues | derive + persist provenance | DESIGN FIELD |
| closureReason | normalized closure rationale | final state + release/close bugnotes | infer + compose | HPO-CONFIRMED RULE |

## AI Agent Fields
- riskScore
- nextBestActions

```

## Quality Gate Before Version Release

- No property without source or explicit design/derived/composed declaration.
- No relation without source evidence and mapping rule.
- Open confirmations listed explicitly.
- `.md` and `.yaml` versions match.
- `.mmd` refreshed for the same version.
