# Design Guidance v1.0

## 1. Purpose

Provide a practical modeling standard for agentic workflow design that keeps domain semantics stable while enabling application-level automation.

## 2. Scope

- Cross-application workflow modeling
- Data model + state/event/action modeling
- Human and agent interaction boundaries

## 3. Pattern A: 4-Layer Design Pattern

### Layer 1: Core Domain Data Model

- Goal: define stable business entities and canonical relations.
- Content: entities, properties, cardinality, link keys, source traceability.
- Rule: keep app-specific UI fields out of this layer.

### Layer 2: State Model

- Goal: define workflow-critical lifecycle states on selected entities.
- Content: finite state enums, transitions, transition triggers, state owners.
- Rule: define states only for workflow-gating, multi-step, operationally useful transitions.

### Layer 3: Interaction Model (Event-Action-DataObject)

- Goal: describe who did what, on which application object, when, and what event was emitted.
- Content: events, actions, actors, target objects, sequence flows, event semantics.
- Rule: treat events as meaningful state transitions; attach states to objects.

### Layer 4: Automation Policy Model

- Goal: define what the agent may read/write/execute and under what conditions.
- Content: touch policies (`agent_managed`, `agent_suggest_only`, `human_only`), trigger rules, exception handling, fallback actions.
- Rule: every external write path must have explicit policy and evidence requirement.

## 4. Pattern B: Specific 2-Layer Data Model Pattern

### Layer B1: Canonical Core Layer

- Stable business model (`Top3Case`, `BranchRequest`, `Branch`, `Issue`, etc.)
- Canonical links and normalized keys
- System-independent semantics

### Layer B2: Interface/Presentation Adapter Layer

- Application/page contracts (`Mantis.Top3CasePage`, `Jenkins.BranchRequestPage`, `Outlook.EmailNotify`)
- Field mappings: app field <-> canonical property
- Human-mimic interaction guidance for agent actions

### Practical Guardrails

1. Business meaning stays in Canonical Core Layer.
2. UI selectors/form fields/page behaviors stay in Interface Layer.
3. App-specific temporary fields do not enter canonical entities unless they are true business facts.
4. Each mapped field must specify source, owner, and write policy.

## 5. Input Artifacts and Output Artifacts

### 5.1 Inputs (Required)

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

### 5.2 Outputs (Required)

1. Canonical data model
- `working/<product>/preview/data-model-<scope>-vX.Y.md`
- `working/<product>/preview/data-model-<scope>-graph-vX.Y.yaml`
- `working/<product>/preview/data-model-<scope>-graph-vX.Y.mmd`

2. State model
- `working/<product>/preview/state-model-<scope>-vX.Y.md`

3. Interaction model
- `working/<product>/preview/event-action-dataobj-flow.md`
- Optional normalization: `working/<product>/preview/workflow-interaction-events-vX.Y.md`

4. Automation policy model
- `working/<product>/preview/automation-policy-<scope>-vX.Y.md`

5. Governance artifacts
- `working/<product>/progress.md` (start/in-progress/breakpoint/completion logs)
- `working/<product>/sparks.md` (deferred ideas and future modeling candidates)

## 6. Minimal Documentation Contract per Version

Each modeling version must include:

1. Version metadata and source-of-truth pointers
2. Coverage dashboard (what is reviewed/pending)
3. Property source checks
4. Relation source checks
5. Derivation/composition rules
6. Open confirmations
7. Decision log

## 7. Quality Checklist

1. Canonical vs adapter fields are clearly separated.
2. No duplicated semantics without explicit canonical/snapshot rule.
3. Events map to object-level state transitions.
4. Interaction steps identify actor, action, object, emitted event.
5. External write permissions are explicit and policy-bound.
6. Markdown, YAML, Mermaid artifacts are synchronized.

## 8. References (Pattern Names)

Comparable industry patterns:
- Canonical Data Model + Anti-Corruption Layer
- Domain Model + View/DTO Model
- Page Object Model (RPA/testing style)
- System-of-Record + Integration Adapter
