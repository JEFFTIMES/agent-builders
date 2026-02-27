# Automation Policy Model - top3-workflow v4.0

## 0. Metadata
- Product: `agent-top3pm`
- Version: `v4.0`
- Last Updated: `2026-02-26`
- Scope: `AI-agent read/write/execute boundaries across Mantis, Jenkins, Outlook, Teams, Phabricator, InfoSite`
- Related Models:
  - `working/agent-top3pm/preview/data-model-core-top3-workflow-v4.0.md`
  - `working/agent-top3pm/preview/data-model-adapter-top3-workflow-v4.0.md`
  - `working/agent-top3pm/preview/state-model-top3-workflow-v4.0.md`
  - `working/agent-top3pm/preview/event-action-dataobj-flow-top3-workflow-v4.0.md`

## 1. Policy Levels
- `agent_managed`: AI agent can write directly through approved tool path.
- `agent_suggest_only`: AI agent proposes values/actions; human applies externally.
- `human_only`: AI agent must not write.
- `system_sync_only`: AI agent reads and syncs only.

## 2. Policy Matrix (Field/Action)
| Field or Action | Policy | Tool Path | Human Approval Requirement |
| --- | --- | --- | --- |
| Read Mantis ticket fields (`Top3Case`, `NFR`, `Issue`) | `system_sync_only` | Mantis page scraping/API | No |
| Write Mantis `Top3Case.status` / `ETA` | `agent_managed` | Mantis edit workflow | Required for major status transitions (`solution sent`, `closed`) |
| Write Mantis `Branch Merge List` | `agent_managed` | Mantis edit workflow | Yes |
| Compose `Top3Case.caseSummary` | `agent_suggest_only` | internal generation | PM review required before external writeback |
| Compose `Top3Case.closureReason` | `agent_suggest_only` | internal generation | PM review required |
| Parse and sync Mantis bugnotes | `system_sync_only` | bugnote section parser | No |
| Fill Jenkins branch request form | `agent_managed` | `Jenkins.BranchRequestFormPage` submit | Yes |
| Fill Jenkins build parameters and matrix | `agent_managed` | `Jenkins.BuildWithParametersPage` submit | Yes |
| Toggle build switches (`RUN_DB_UPDATE`, `TRIAL`, `SA`) | `agent_managed` | Jenkins build form | Yes |
| Sync branch/build metadata from Jenkins pages | `system_sync_only` | Jenkins page scraping | No |
| Parse branch request/approved emails | `system_sync_only` | Outlook mailbox sync | No |
| Parse build success/failure emails | `system_sync_only` | Outlook mailbox sync | No |
| Update `BranchRequest.requestStatus` from email events | `agent_managed` (internal) | internal state update | No |
| Index InfoSite artifact rows | `system_sync_only` | InfoSite crawler | No |
| Sync Phabricator task status/checklist | `system_sync_only` | Phabricator page parsing/API | No |
| Draft `ReleaseNote.content` | `agent_suggest_only` | internal generation | PM approval required |
| Publish release note/closure note | `human_only` | Mantis write action | Always |
| Teams thread summarization and action extraction | `system_sync_only` | Teams transcript parser | No |
| Identity normalization (`Person.personId`) | `agent_managed` (internal) | internal resolver | No |

## 3. Trigger Rules
1. Trigger `branch request sync` when branch request email arrives.
2. Trigger `branch approval sync` when approval email arrives.
3. Trigger `build result sync` when build success/failure email arrives.
4. Trigger `artifact sync` when a success build has InfoSite URL.
5. Trigger `validation sync` when Phabricator task status/comment changes.
6. Trigger `case closure derivation` only after release/close bugnote appears.

## 4. Exception and Fallback Policy
| Failure Point | Detection | Fallback Action | Escalation |
| --- | --- | --- | --- |
| Branch request not approved within SLA | no approval event after request email | keep `BranchRequest` in pending state and send reminder | PM |
| Build failure notification | `BuildRun.result=failure` | capture console URL and create blocker summary | PM + Dev lead |
| InfoSite artifacts missing after success | no artifact rows found for expected build tag | retry crawl with backoff and alternate region path | PM + DevOps |
| Phabricator checklist missing | task status changed but no checklist links | mark `ValidationRecord` blocked | QA owner |
| Ambiguous `.msg` parsing | missing build URLs in parsed content | fallback to Jenkins build history page lookup | PM + DevOps |

## 5. Audit Requirements
- Every external write attempt must store: `who`, `when`, `target`, `before`, `after`, `evidence_ref`, `policy_level`.
- Every agent-derived field must store: `inputs`, `prompt version/rule`, `timestamp`, `review status`.
- Every status transition must store: `source event id` and `adapter source path`.

## 6. Open Confirmations
| Item | Why Open | Proposed Resolution | Owner |
| --- | --- | --- | --- |
| Auto-write scope for Mantis status updates | operational risk if agent updates status prematurely | keep approval gate for non-trivial status writes | Peter + HPO |
| Jenkins submission autonomy for production branches | high impact action | keep explicit PM confirmation before submit | Peter + HPO |

## 7. Decision Log
- [2026-02-26] Added explicit write-boundary contract for all major external touch points.
- [2026-02-26] Kept `caseSummary`, `closureReason`, and release-note drafting as `agent_suggest_only` to preserve PM governance.
