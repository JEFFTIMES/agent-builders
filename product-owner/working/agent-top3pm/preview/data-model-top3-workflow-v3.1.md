# Data Model Discovery - top3-workflow v3.1 - agent-top3pm

- Product: `agent-top3pm`
- Domain / Bounded Context: `Top3 PM cross-system delivery workflow (Mantis/Teams/Jenkins/Outlook/InfoSite/Phabricator)`
- Discovery Scope Boundary: `Managed objects, relations, link keys, and agent derivation rules for TOP3 case orchestration`
- Entity Lifecycle Span (start -> end): `Top3Case intake -> branch/build -> QA validation -> release/closure`
- Version: `v3.1`
- Last Updated (time, owner): `2026-02-26`, `Peter (PD)`
- Source Corpus Snapshot (systems/pages/files): `working/agent-top3pm/assets/top3apps/*.html`, `working/agent-top3pm/assets/top3apps/*.msg`, `working/agent-top3pm/assets/top3apps/teams-chat-core-1229978.md`
- Source-of-Truth Model File: `working/agent-top3pm/preview/data-model-top3-workflow-v3.1.md`
- Graph Model File (canonical YAML): `working/agent-top3pm/preview/data-model-top3-workflow-graph-v3.1.yaml`
- Graph View File (Mermaid): `working/agent-top3pm/preview/data-model-top3-workflow-graph-v3.1.mmd`

## 0. Data Modeling Purposes

1. Build a canonical cross-application entity model for PM/QA/Dev collaboration data.
2. Preserve property-level source traceability to original systems/pages/fields for reliable sync.
3. Define agent-operable semantics (derivation rules, writable boundaries, and relation traversal) for agentic orchestration.
4. Prepare the storage/ontology foundation for a single agent view across duplicated and scattered enterprise applications.

## 1. Coverage Dashboard

| Entity              | Property Check | Relation Check | Status       | Open Items                            |
| ------------------- | -------------- | -------------- | ------------ | ------------------------------------- |
| Top3Case            | Done           | Done           | Reviewed     | None                                  |
| NFR                 | Done           | Done           | Reviewed     | None                                  |
| Issue               | Done           | Done           | Reviewed     | None                                  |
| BugNote             | Done           | Done           | Reviewed     | None                                  |
| ActionHistory       | Done           | Done           | Reviewed     | None                                  |
| Top3Build           | Done           | Done           | Reviewed     | None                                  |
| Patch               | Done           | Done           | Reviewed     | None                                  |
| BranchRequestPage   | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| BranchRequest       | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| EmailNotify         | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| Branch              | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| BuildRequestPage    | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| BuildRequest        | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| BuildRun            | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| PhabricatorTask     | Done           | Done           | Reviewed     | None                                  |
| ValidationRecord    | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| ReleaseNote         | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| Person              | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| Team                | Pending        | Pending        | Not Reviewed | Add property + relation source checks |
| CommunicationThread | Pending        | Pending        | Not Reviewed | Add property + relation source checks |

## 1.1 Strict Check Result (v3.1)

| Scope | Finding | Normalization Decision | Result |
| ----- | ------- | ---------------------- | ------ |
| `BranchRequestPage` vs `BranchRequest` | Form fields and request payload overlapped without explicit transfer rule | Keep form fields in `BranchRequestPage`; keep normalized payload in `BranchRequest`; connect via `submits` relation | Applied |
| Branch request flow | Legacy semantics mixed page-access and request-submit operations | Use relation chain `Person -> accessesBranchRequestPage -> BranchRequestPage -> submits -> BranchRequest -> generatesBranch -> Branch` | Applied |
| Email status sync | Email semantics existed only in prose and were not graph-synced | Model `EmailNotify` entity and relation `updatesStatus -> BranchRequest`; add derivation rules for event classification and status mapping | Applied |
| Naming consistency | Mixed `pageURL/requestID` and non-normalized labels | Normalize to `pageUrl/requestId`; keep relation and property names camelCase | Applied |
| Cross-artifact consistency | Markdown-only additions in prior version caused graph drift | Synchronize entity/relation additions across `v3.1.md`, `v3.1.yaml`, `v3.1.mmd` | Applied |

## 1.2 External Data Touch Policy (First Round)

- Tag name: `external_data_touch_policy`
- Applies to: agent touch permission on existing external applications (for non-external/internal fields, first-round default remains conservative `system_sync_only` unless overridden).
- Options:
- `agent_managed`: agent can directly write/modify external data field through approved tools.
- `human_only`: agent must not write; human updates externally.
- `agent_suggest_only`: agent proposes value/action; human confirms and applies externally.
- `system_sync_only`: implicit default when no tag is present; agent reads and syncs from source system but does not directly write external source field.
- Note: no tag means `external_data_touch_policy=system_sync_only`.

## 2. Entity Discovery Cards

### Entity: `ProductFamily`



### Entity: `Top3Case`

#### Surfacing Applications

- Mantis: Top3 Ticket/NFR Ticket/Issue Ticket
- Email: ticket change update notification
- Jenkins: branch request page
- PMDB: pmdb doc 
- Phab: QA ticket
- Teams: Chat Group Title

#### Properties

- `caseId` (string, primary key)
- `title` (string, from Mantis Summary)
- `caseCategory` (enum, refer to `top3-case-category.md`)
- `ETA` (datetime, from Mantis ETA) [external_data_touch_policy=agent_managed]
- `status` (enum: `new`/`investigating`/`fix needed`/`solution sent`/`bumped out`) [external_data_touch_policy=agent_managed]
- `severity` (enum: `s1`/`s2`/`s3`/`s4`) [external_data_touch_policy=agent_managed]
- `createdAt` (datetime, from Mantis `Date Submitted`)
- `updatedAt` (datetime, from Mantis `Last Update`)
- `mantisUrl` (string)
- `description` (text, from Mantis `Description`)
- `caseSummary` (text, AI-Agent inferred from reviewing cross-app project context: critical issues, obstacles, turning points, key persons and contributions)
- `customerName` (string, from Mantis `Affected Customer`)
- `pmOwnerId` (personId)
- `qaOwnerId` (personId)
- `devOwnerId` (personId) [external_data_touch_policy=agent_suggest_only]
- `supportOwnerIds` (list[personId])
- `branch` (string, from `Branch Merge List`) [external_data_touch_policy=agent_managed]
- `hardwareModels` (list[enum], from `Hardware Model`)
- `currentPhase` (enum: investigation/repro/dev/build/qa/release)
- `currentPhaseSource` (enum: manual/derived_from_bugnote)
- `currentPhaseEvidenceNoteId` (string, nullable)
- `closedAt` (datetime, nullable, inferred from BugNote headline `---Release---` or `---Close---` defined in `preview/bugnote-headlines.md`)
- `closureReason` (enum, nullable, AI-agent inferred/composed from final state and closing evidence)

#### Outgoing Relations

- `hasNFR -> NFR` (`0..*`)
- `hasIssue -> Issue` (`0..*`)
- `ownedBy -> Person(PM)` (`1..1`)
- `hasParticipant -> Person` (contextual)
- `hasTeam -> Team` (`2..2`)
- `hasQaOwner -> Person(QA)` (`0..1`)
- `hasDevOwner -> Person(Dev)` (`0..1`)
- `hasSupportOwner -> Person(Support)` (`0..*`)
- `hasBugNote -> BugNote` (`0..*`)
- `hasActionHistory -> ActionHistory` (`0..*`)
- `usesBranch -> Branch` (`0..*`)
- `publishesDeliverable -> ReleaseNote` (`0..*`)

#### Incoming Relations

- `targets <- BranchRequest`
- `references <- BugNote`

#### Property Source Check (Double-Checked)

- `caseId` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`bug_id` in case URL/context). `CONFIRMED`
- `title` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Summary`). `CONFIRMED`
- `caseCategory` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Category`), enum list from `working/agent-top3pm/assets/top3-case-category.md`. `CONFIRMED`
- `ETA` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`ETA`). `CONFIRMED`
- `status` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Status`). `CONFIRMED`
- `severity` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Severity`). `CONFIRMED`
- `createdAt` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Date Submitted`). `CONFIRMED`
- `updatedAt` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Last Update`). `CONFIRMED`
- `mantisUrl` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (case URL pattern). `CONFIRMED`
- `description` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Description`). `CONFIRMED`
- `caseSummary` <- PM-authored summary field in target model; not a direct Mantis field. `HPO-CONFIRMED DESIGN FIELD`
- `customerName` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Affected Customer`). `CONFIRMED`
- `pmOwnerId` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Assigned PM`) as current workaround. `HPO-CONFIRMED WORKAROUND`
- `qaOwnerId` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`QA Assignee`). `CONFIRMED`
- `devOwnerId` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Assigned To` as Dev/Dev lead per workflow). `HPO-CONFIRMED INTERPRETATION`
- `supportOwnerIds` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Support/Field Contact`). `CONFIRMED`
- `branch` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Branch Merge List`). `CONFIRMED`
- `hardwareModels` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`Hardware Model`). `CONFIRMED`
- `currentPhase` <- derived lifecycle state from workflow + bugnote/action signals; not direct Mantis field. `HPO-CONFIRMED DESIGN FIELD`
- `currentPhaseSource` <- model provenance field. `DESIGN FIELD`
- `currentPhaseEvidenceNoteId` <- model provenance field. `DESIGN FIELD`
- `closedAt` <- inferred from BugNote `createdAt` where `headlineRaw` is `---Release---` or `---Close---` per `working/agent-top3pm/preview/bugnote-headlines.md`. `HPO-CONFIRMED RULE`
- `closureReason` <- AI Agent infers and composes from final state + release/close bugnote evidence for normalized closure rationale. `HPO-CONFIRMED RULE`

#### Relation Source Check (Double-Checked)

- `hasNFR` <- `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html` (`NFR` field). `CONFIRMED`
- `hasIssue` <- Mantis-linked issue IDs in case content and process references from HPO workflow. `HPO-CONFIRMED INTERPRETATION`
- `ownedBy(PM)` <- workflow governance role; current value sourced from Mantis `Assigned PM` as workaround. `HPO-CONFIRMED WORKAROUND`
- `hasParticipant` <- HPO-defined participators and Teams collaboration pattern. `HPO-CONFIRMED INTERPRETATION`
- `hasTeam` <- HPO-defined Core/All two-tier Teams structure. `HPO-CONFIRMED INTERPRETATION`
- `hasQaOwner` <- `QA Assignee` in Mantis. `CONFIRMED`
- `hasDevOwner` <- `Assigned To` in Mantis plus HPO role interpretation. `HPO-CONFIRMED INTERPRETATION`
- `hasSupportOwner` <- `Support/Field Contact` in Mantis. `CONFIRMED`
- `hasBugNote` <- `Bug Notes` section in Mantis. `CONFIRMED`
- `hasActionHistory` <- `Action History` section in Mantis. `CONFIRMED`
- `publishesDeliverable` <- bug notes (`---Release---`) + workflow release step. `HPO-CONFIRMED INTERPRETATION`

### Entity: `NFR`

#### Surfacing Applications

- Mantis: Top3 Ticket/NFR Ticket/Issue Ticket
- Email: ticket change update notification
- PMDB: pmdb doc 
- Phab: QA ticket
- Teams: Chat messages


#### Properties

- `nfrId` (string, primary key)
- `product` (string, from Mantis `Product` field)
- `solutionRaw` (string, from Mantis `Solution` field) [external_data_touch_policy=agent_managed]
- `solutionTags` (list[string], derived by splitting `solutionRaw` on commas and trimming whitespace)
- `summary` (string, from Mantis `Summary`)
- `description` (text)
- `severity` (string, from Mantis `Severity`)
- `reporterId` (personId, from Mantis `Reporter`)
- `dateSubmitted` (datetime, from Mantis `Date Submitted`)
- `lastUpdate` (datetime, from Mantis `Last Update`)
- `eta` (datetime/date, from Mantis `ETA`)
- `affectedCustomer` (string, from Mantis `Affected Customer`)
- `salesforceOppIds` (list[string], from Mantis `Salesforce Opp IDs (comma separated)`)
- `salesforceAccountIds` (list[string], from Mantis `Salesforce Account IDs (comma separated)`)
- `additionalInformation` (text, from Mantis `Additional Information`)
- `status` (enum)
- `conclusion` (text, AI-agent inferred/composed after end-to-end NFR ticket review)
- `referredIssueIds` (list[string], derived from NFR-local bug notes and description parsing)
- `duplicateNfrId` (string, nullable, from ticket `Duplicate ID`) [external_data_touch_policy=agent_managed]
- `referredNfrIds` (list[string], derived from NFR-local bug notes and description parsing)

#### Outgoing Relations

- `reportedBy -> Person` (`0..1`, from `reporterId`)
- `hasBugNote -> BugNote` (`0..*`)
- `references -> NFR` (`0..*`, from `referredNfrIds`)
- `references -> Issue` (`0..*`, from `referredIssueIds`)
- `duplicateOf -> NFR` (`0..1`, from `duplicateNfrId`)

#### Incoming Relations

- `hasNFR <- Top3Case` (`0..*`)
- `references <- BugNote` (`0..*`)
- `references <- NFR` (`0..*`)
- `references <- Issue` (`0..*`)


#### Property Source Check (Double-Checked)

- `nfrId` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`bug_id` in ticket URL/context). `CONFIRMED`
- `product` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (table label `Product`, value shown as `[NFR] FortiOS`). `CONFIRMED`
- `solutionRaw` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (row label `Solution`, value `MSSP,NGFW,Telco`). `CONFIRMED`
- `solutionTags` <- derived from `solutionRaw` by comma-split and trim normalization. `HPO-CONFIRMED RULE`
- `summary` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`Summary`). `CONFIRMED`
- `description` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`Description`). `CONFIRMED`
- `severity` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`Severity`). `CONFIRMED`
- `reporterId` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`Reporter`, e.g., `Tom Walker (twalker)`), normalize account token to `Person.personId`. `CONFIRMED`
- `dateSubmitted` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`Date Submitted`). `CONFIRMED`
- `lastUpdate` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`Last Update`). `CONFIRMED`
- `eta` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`ETA`). `CONFIRMED`
- `affectedCustomer` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`Affected Customer`). `CONFIRMED`
- `salesforceOppIds` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`Salesforce Opp IDs (comma separated)`). `CONFIRMED`
- `salesforceAccountIds` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`Salesforce Account IDs (comma separated)`). `CONFIRMED`
- `additionalInformation` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`Additional Information`). `CONFIRMED`
- `referredIssueIds` <- AI-agent extraction from `NFR.hasBugNote -> BugNote.rawText` and `NFR.description` using patterns (`#id`, `mantis#id`, `bug_id=id`) constrained to Issue ticket type. `HPO-CONFIRMED RULE`
- `referredNfrIds` <- AI-agent extraction from `NFR.hasBugNote -> BugNote.rawText` and `NFR.description` using patterns (`#id`, `mantis#id`, `bug_id=id`) constrained to NFR ticket type. `HPO-CONFIRMED RULE`
- `duplicateNfrId` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`Duplicate ID`). `CONFIRMED`
- `conclusion` <- AI Agent infers and composes NFR-level conclusion from whole NFR ticket evidence (`Summary`, `Description`, `Additional Information`, status/release context). `HPO-CONFIRMED RULE`
- `status` <- `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html` (`Status`, current value `Concept Commit`). `CONFIRMED`

#### Relation Source Check (Double-Checked)

- `hasNFR <- Top3Case` <- `Top3Case` parent ticket links/references NFR records through the Mantis `NFR` linkage field and workflow mapping artifacts. `HPO-CONFIRMED INTERPRETATION`
- `references <- BugNote` <- inverse mapping of `BugNote references -> NFR` via parsed `mentionedTicketIds` (`#id`, `mantis#id`, `bug_id=id`) and NFR ticket-type resolution. `HPO-CONFIRMED INTERPRETATION`
- `reportedBy -> Person` <- map `NFR.reporterId` from Mantis `Reporter` to `Person.personId` (company account id token). `CONFIRMED`
- `hasBugNote -> BugNote` <- NFR ticket `Bug Notes` section entries keyed by parent `nfrId` (`bug_id`) and bugnote IDs. `CONFIRMED`
- `references -> NFR` <- resolve from `NFR.referredNfrIds` after ticket-type filtering to NFR. `HPO-CONFIRMED RULE`
- `references -> Issue` <- resolve from `NFR.referredIssueIds` after ticket-type filtering to Issue. `HPO-CONFIRMED RULE`
- `duplicateOf -> NFR` <- map `NFR.duplicateNfrId` to target `NFR.nfrId` when value exists. `CONFIRMED`

#### Agent Actions (First Round)

- `ingest_from_mantis`: scrape NFR core fields (`product`, `solutionRaw`, `summary`, `description`, `status`, IDs, timestamps), normalize person/ticket IDs, and upsert into agent DB.
- `sync_bugnote_links`: parse `NFR.hasBugNote -> BugNote.rawText` and `NFR.description` to refresh `referredIssueIds` and `referredNfrIds`.
- `compose_conclusion`: infer concise NFR conclusion from full ticket evidence and store in internal field `NFR.conclusion`.
- `writeback_policy`:
  `solutionRaw`, `duplicateNfrId`: `agent_managed` (agent can write through approved Mantis tool path).
  `conclusion`, `solutionTags`, `referredIssueIds`, `referredNfrIds`: internal synthesized fields (no direct external writeback).

### Entity: `Issue`

#### Surfacing Applications

- Mantis: Top3 Ticket/NFR Ticket/Issue Ticket
- Email: ticket change update notification
- Phab: QA ticket
- Teams: Chat messages

#### Properties

- `issueId` (string, primary key)
- `title` (string)
- `description` (text)
- `category` (string, from Mantis `Category`) [external_data_touch_policy=agent_managed]
- `severity` (string, from Mantis `Severity`) [external_data_touch_policy=agent_managed]
- `reproducibility` (string, from Mantis `Reproducibility`)
- `reporterId` (personId, from Mantis `Reporter`)
- `keyword` (string, nullable, from Mantis `Keyword`)
- `reproducedById` (personId, nullable, from Mantis `Reproduced By`)
- `reportedVersion` (string, nullable, from Mantis `Reported Version`)
- `earliestBuildNumber` (string, nullable, from Mantis `Earliest Build Number`)
- `referredIssueIds` (list[string], derived from issue-local bug note parsing)
- `duplicateIssueId` (string, nullable, from Mantis `Duplicate ID`) [external_data_touch_policy=agent_managed]
- `referredNfrIds` (list[string], derived from issue-local bug note parsing)
- `assignedDevId` (personId, from Mantis `Assigned To`) [external_data_touch_policy=agent_managed]
- `qaAssigneeId` (personId, nullable, from Mantis `QA Assignee`)
- `fixSchedule` (string, nullable)
- `resolvedIn` (string, nullable)
- `conclusion` (text, AI-agent inferred/composed after end-to-end issue ticket review, summarize expected/observed behavior and discrepancy)
- `status` (enum)

#### Outgoing Relations

- `references -> Issue` (`0..*`, from `referredIssueIds`)
- `references -> NFR` (`0..*`, from `referredNfrIds`)
- `duplicateOf -> Issue` (`0..1`, from `duplicateIssueId`)
- `fixedBy -> Person(Dev)` (`0..1`)
- `hasQaAssignee -> Person(QA)` (`0..1`)
- `hasBugNote -> BugNote` (`0..*`)


#### Incoming Relations

- `hasIssue <- Top3Case` (`0..*`)
- `references <- BugNote` (`0..*`)
- `references <- NFR` (`0..*`) 
- `validatesIssue <- ValidationRecord`

#### Property Source Check (Double-Checked)

- `issueId` <- linked Mantis issue ticket ID (`bug_id`) from issue page URL/context in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `title` <- issue `Summary` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `description` <- issue `Description` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `category` <- issue `Category` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `severity` <- issue `Severity` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `reproducibility` <- issue `Reproducibility` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `reporterId` <- issue `Reporter` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`; normalize account token to `Person.personId`. `CONFIRMED`
- `keyword` <- issue `Keyword` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `reproducedById` <- issue `Reproduced By` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`; normalize account token to `Person.personId`. `CONFIRMED`
- `reportedVersion` <- issue `Reported Version` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `earliestBuildNumber` <- issue `Earliest Build Number` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `referredIssueIds` <- AI-agent extraction from linked issue bug notes (`Issue.hasBugNote -> BugNote.rawText`) using text patterns (`#id`, `mantis#id`, `bug_id=id`) in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`; parsed values are deduplicated issue IDs linked back to the current issue. `HPO-CONFIRMED RULE`
- `referredNfrIds` <- AI-agent extraction from linked issue bug notes (`Issue.hasBugNote -> BugNote.rawText`) using text patterns (`#id`, `mantis#id`, `bug_id=id`), constrained to NFR ticket type. `HPO-CONFIRMED RULE`
- `duplicateIssueId` <- issue `Duplicate ID` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `assignedDevId` <- issue `Assigned To` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`; normalize account token to `Person.personId`. `CONFIRMED`
- `qaAssigneeId` <- issue `QA Assignee` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`; normalize account token to `Person.personId`. `CONFIRMED`
- `fixSchedule` <- issue `Fix Schedule` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `resolvedIn` <- issue `Resolved In` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`
- `conclusion` <- AI Agent infers and composes issue-level conclusion from whole issue ticket evidence (`Summary`, `Description`, `Bug Notes`, assignment/status progression, and resolution fields). `HPO-CONFIRMED RULE`
- `status` <- issue `Status` field in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`. `CONFIRMED`

#### Relation Source Check (Double-Checked)

- `references -> Issue` <- resolve outgoing issue links from `Issue.referredIssueIds` extracted from linked `Issue.hasBugNote -> BugNote.rawText` patterns (`#id`, `mantis#id`, `bug_id=id`), constrained to Mantis issue ticket type by lookup. `HPO-CONFIRMED RULE`
- `references -> NFR` <- resolve outgoing NFR links from `Issue.referredNfrIds` extracted from linked `Issue.hasBugNote -> BugNote.rawText` patterns (`#id`, `mantis#id`, `bug_id=id`), constrained to Mantis NFR ticket type by lookup. `HPO-CONFIRMED RULE`
- `duplicateOf -> Issue` <- map `Issue.duplicateIssueId` from Mantis `Duplicate ID` to target `Issue.issueId` when value exists. `CONFIRMED`
- `fixedBy -> Person(Dev)` <- map `Issue.assignedDevId` from Mantis `Assigned To` to `Person.personId` (company account id token). `CONFIRMED`
- `hasQaAssignee -> Person(QA)` <- map `Issue.qaAssigneeId` from Mantis `QA Assignee` to `Person.personId` (company account id token). `CONFIRMED`
- `hasBugNote -> BugNote` <- issue `Bug Notes` section entries in `working/agent-top3pm/assets/top3apps/1241381 -- Mantis.html` and `working/agent-top3pm/assets/top3apps/1241309 -- Mantis.html`, keyed by parent issue `bug_id` with note IDs. `CONFIRMED`
- `hasIssue <- Top3Case` <- Top3 case page links to issue tickets (e.g., `1241381`, `1241309`) in `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html`, aligned with `Top3Case.hasIssue` source-check and worked-instance mapping in `working/agent-top3pm/preview/x-app-mo-data-handoff-v0.3.md`. `HPO-CONFIRMED INTERPRETATION`
- `references <- BugNote` <- inverse mapping of `BugNote references -> Issue` through `mentionedTicketIds` parsing (`#id`, `mantis#id`, `bug_id=id`) and Mantis ticket-type resolution, per BugNote relation source-check. `HPO-CONFIRMED INTERPRETATION`
- `validatesIssue <- ValidationRecord` <- validation model defines `ValidationRecord validatesIssue -> Issue` with QA tracking context in `working/agent-top3pm/preview/x-app-mo-data-handoff-v0.3.md` and current workflow model. `HPO-CONFIRMED INTERPRETATION`

### Entity: `BugNote`

#### Surfacing Applications

- Mantis: Top3 Ticket/NFR Ticket/Issue Ticket

#### Properties

- `bugNoteId` (string, primary key)
- `ticketId` (string, parent Mantis ticket id)
- `authorDisplay` (string)
- `headlineType` (enum: update/summary/release/close/other)
- `headlineRaw` (string, nullable)
- `rawNote` (text)
- `rawHtml` (text)
- `rawText` (text) 
- `noteKind` (enum: content_update/reminder/reply/system)
- `mentionedMantisIds` (list[string], from `#<mantisId>` extraction)
- `mentionedTicketIds` (list[string], from `#<mantisId>`/`mantis#<mantisId>`/`bug_id=<mantisId>`)
- `phabTaskIds` (list[string], from Phabricator lookup by `Top3Case.caseId` + QA owner identity)
- `createdAt` (datetime)
- `extractAt` (datetime, agent scrape timestamp; on revisit compare with Mantis note `createdAt` to detect updates)
- `bugNoteEditUrl` (string, derived shortcut URL)
- `bugNoteDeleteUrl` (string, derived shortcut URL)

#### Outgoing Relations

- `references -> Top3Case` (`0..*`)
- `references -> Top3Build` (`0..*`)
- `references -> NFR` (`0..*`)
- `references -> Issue` (`0..*`)
- `references -> PhabricatorTask` (`0..*`)
- `authoredBy -> Person` (`1..1`, when author mapping is parseable)

#### Incoming Relations

- `hasBugNote <- Top3Case` (`0..*`)
- `hasBugNote <- Issue` (`0..*`)

#### Property Source Check (Double-Checked)

- `bugNoteId` <- Mantis bugnote operation URLs (`bugnote_edit_page.php?bugnote_id=...`, `bugnote_delete.php?bugnote_id=...`). `CONFIRMED`
- `ticketId` <- Mantis ticket context (`bug_id` in page URL and reply links). `CONFIRMED`
- `authorDisplay` <- Bugnote header display (`Name (account)` and `data-user`). `CONFIRMED`
- `createdAt` <- Bugnote header timestamp shown beside author. `CONFIRMED`
- `rawHtml`/`rawText`/`headlineRaw` <- Bugnote content block (`<td class="bugnote-note-public"><tt>...</tt></td>`). `CONFIRMED`
- `headlineType` <- derived from standardized headline patterns (e.g., `--- Update ---`) using `preview/bugnote-headlines.md`. `HPO-CONFIRMED RULE`
- `mentionedTicketIds` <- parsed from bugnote content patterns (`#id`, `mantis#id`, `bug_id=id`). `CONFIRMED`
- `phabTaskIds` <- queried from Phabricator by `Top3Case.caseId` + QA identity, then linked back to case/notes. `HPO-CONFIRMED RULE`
- `extractAt` <- capture timestamp generated by scraper; compare against page note datetime on subsequent visits. `HPO-CONFIRMED RULE`
- `bugNoteEditUrl`/`bugNoteDeleteUrl` <- derived from Mantis bugnote operation links using `bugNoteId`. `CONFIRMED`

#### Relation Source Check (Double-Checked)

- `references -> Top3Case` <- Bugnote parent ticket context from `ticketId` (`bug_id` in Mantis page URL + bugnote operation links). `CONFIRMED`
- `references -> NFR` <- `mentionedTicketIds` parsed from bugnote content (`#id`, `mantis#id`, `bug_id=id`) and resolved to NFR records via Mantis ticket lookup. `HPO-CONFIRMED INTERPRETATION`
- `references -> Issue` <- `mentionedTicketIds` parsed from bugnote content (`#id`, `mantis#id`, `bug_id=id`) and resolved to bug-fix issue records via Mantis ticket lookup. `HPO-CONFIRMED INTERPRETATION`
- `references -> PhabricatorTask` <- `phabTaskIds` resolved by AI agent Phabricator query using `Top3Case.caseId` + QA identity, then linked back to notes. `HPO-CONFIRMED RULE`
- `authoredBy -> Person` <- bugnote header author identity (`Name (account)` / `data-user`) mapped to `Person.personId` (company account id). `CONFIRMED`
- `hasBugNote <- Top3Case` <- Mantis `Bug Notes` section under parent ticket, keyed by `ticketId` and bugnote IDs. `CONFIRMED`
- `hasBugNote <- Issue` <- Mantis issue ticket `Bug Notes` section under parent issue `bug_id`, keyed by bugnote IDs. `CONFIRMED`



### Entity: `ActionHistory`

#### Surfacing Applications

- Mantis: Top3 Ticket

#### Properties

- `actionHistoryId` (string, primary key, deterministic key by `caseId + sourceSection`)
- `caseId` (string, parent Top3 case id)
- `sourceSystem` (enum: Mantis)
- `sourceSection` (string, default: `Action History`)

#### Incoming Relations

- `hasActionHistory <- Top3Case` (`0..*`)

#### Outgoing Relations

- `hasTop3Build -> Top3Build` (`0..*`)

#### Property Source Check (Double-Checked)

- `caseId` <- Mantis page context (`bug_id`) of parent Top3 ticket. `CONFIRMED`
- `sourceSystem` <- fixed by source system of section (`Mantis`). `CONFIRMED`
- `sourceSection` <- fixed literal section name `Action History`. `CONFIRMED`

#### Relation Source Check (Double-Checked)

- `hasActionHistory <- Top3Case` <- `Action History` section in Mantis case page keyed by parent `bug_id`. `CONFIRMED`
- `hasTop3Build -> Top3Build` <- grouped by each `Top3Build` row marker in Action History section. `HPO-CONFIRMED INTERPRETATION`



### Entity: `Top3Build`

#### Surfacing Applications

- Mantis: Top3 Ticket/NFR Ticket/Issue Ticket description field, Action History field, Bug notes
- Jenkins: Build Request Page
- Email: build run success update notification
- Phab: QA ticket
- Teams: Chat messages

#### Properties

- `top3BuildId` (string, primary key, deterministic key by `generatingBranchName + buildNumber`)
- `generatingBranchName` (string, )
- `buildNumber` (string; value after `Top3Build` label)
- `caseId` (string, parent Top3 case id)
- `buildQualifier` (text, nullable; suffix note such as `for 3800G/3801G`)
- `sequenceNo` (integer; order of build groups in Action History section)
- `sourceSystem` (enum: Mantis, Jenkins, Email)

#### Incoming Relations

- `hasTop3Build <- ActionHistory` (`0..*`)
- `references <- BugNote` (`0..*`)
- `generatesTop3Build <- Branch` (`1..*`)

#### Outgoing Relations

- `hasPatch -> Patch` (`0..*`)

#### Property Source Check (Double-Checked)

- `caseId` <- parent Mantis page context (`bug_id`). `CONFIRMED`
- `buildNumber` <- row where first cell is `Top3Build`, value parsed from next cell text. `CONFIRMED`
- `buildQualifier` <- parenthesized/extra text after build number in same build-marker cell. `HPO-CONFIRMED INTERPRETATION`
- `sequenceNo` <- order of `Top3Build` markers in Action History section. `CONFIRMED`
- `sourceSystem` <- Mantis section context. `CONFIRMED`

#### Relation Source Check (Double-Checked)

- `hasTop3Build <- ActionHistory` <- one Action History container per case, containing all Top3Build groups. `HPO-CONFIRMED INTERPRETATION`
- `hasPatch -> Patch` <- fix rows until next `Top3Build` belong to current build group. `HPO-CONFIRMED RULE`

### Entity: `Patch`

#### Surfacing Applications

- internal data object, used to compose ActionHistory

#### Properties

- `patchId` (string, primary key, deterministic key by `top3BuildId + issueId`)
- `top3BuildId` (string, FK to Top3Build)
- `issueId` (string, mantis issue id fixed by this patch)
- `mergeToMainState` (enum: `merged_to_main`/`not_merged_to_main`/`unknown`)
- `mainBranchPoint` (integer, nullable; numeric merged target when present)
- `mainBranchPointRaw` (string, nullable; extracted token from `fixesIssue -> Issue.resolvedIn`)
- `issueTitle` (string; sourced from `fixesIssue -> Issue.title`)
- `sequenceNo` (integer; order within build group)
- `sourceSystem` (enum: Mantis)
- `sourceSection` (string, default: `Action History`)
- `sourceTicketId` (string)

#### Incoming Relations

- `hasPatch <- Top3Build` (`0..*`)

#### Outgoing Relations

- `fixesIssue -> Issue` (`1..1`, keyed by `issueId`)

#### Property Source Check (Double-Checked)

- `top3BuildId` <- inherited from active build group context while parsing fix rows. `HPO-CONFIRMED RULE`
- `issueId` <- fix row first-column anchor text/link `bug_id`. `CONFIRMED`
- `mainBranchPointRaw` <- extracted from linked `Issue.resolvedIn` (`fixesIssue -> Issue`) by tokenization rule; keep first branchpoint-like token. `HPO-CONFIRMED RULE`
- `mainBranchPoint` <- derived numeric parse of `mainBranchPointRaw`; null when extracted token is non-numeric. `HPO-CONFIRMED RULE`
- `mergeToMainState` <- derived from `mainBranchPointRaw` and `mainBranchPoint`: numeric branchpoint => `merged_to_main`; explicit non-merged token (e.g., `Mantis`) => `not_merged_to_main`; else `unknown`. `HPO-CONFIRMED RULE`
- `issueTitle` <- linked `Issue.title` via `fixesIssue -> Issue` relation. `HPO-CONFIRMED INTERPRETATION`
- `sequenceNo` <- order of fix rows within current Top3Build group. `CONFIRMED`
- `sourceSystem`/`sourceSection`/`sourceTicketId` <- Mantis section context. `CONFIRMED`

#### Relation Source Check (Double-Checked)

- `hasPatch <- Top3Build` <- all fix rows under current Top3Build marker until next marker. `HPO-CONFIRMED RULE`
- `fixesIssue -> Issue` <- `issueId` resolved to Issue entity by Mantis bug_id. `HPO-CONFIRMED INTERPRETATION`

#### Agent Actions (First Round)

- `ingest_action_history_rows`: parse each Top3Build group and upsert patch records (`top3BuildId`, `issueId`, `sequenceNo`, source context).
- `sync_issue_snapshot`: resolve `fixesIssue -> Issue` and refresh `issueTitle` snapshot for quick review views.
- `derive_merge_state`: compute `mainBranchPointRaw`, `mainBranchPoint`, and `mergeToMainState` from linked issue resolution evidence.
- `writeback_policy`:
  all Patch properties are currently `system_sync_only` by default (no direct external writeback in first round).



### Entity: `BranchRequestPage`

#### Surfacing Applications

- jenkins-sjc.corp.fortinet.com/job/Request_branch_GitLab/build?delay=0sec [FortiOS]
- jenkins.corp.fortinet.com/job/Request_branch_GitLab/build?delay=0sec [Others]


#### Properties

- `branchRequestPageId` (string, primary key)
- `pageUrl` (text: jenkins-sjc.corp.fortinet.com/job/Request_branch_GitLab/build?delay=0sec, etc.)
- `GITLAB_TOP_LEVEL_GROUP` (enum) [external_data_touch_policy=agent_managed]
- `GITLAB_PROJECT` (enum) [external_data_touch_policy=agent_managed]
- `MAJOR_VERSION` (enum) [external_data_touch_policy=agent_managed]
- `MINOR_VERSION` (enum) [external_data_touch_policy=agent_managed]
- `NEW_BRANCH_NAME` (text) [external_data_touch_policy=agent_managed]
- `BRANCH_POINT` (enum) [external_data_touch_policy=agent_managed]
- `BRANCH_TYPE` (enum: `R&D`/`Top3`/`NPI`) [external_data_touch_policy=agent_managed]
- `BRANCH_DESCRIPTION` (text) [external_data_touch_policy=agent_managed] (filling a top3case id is mandatory for this field, it's the key but implicit expression of top3case 'hasBranch' the branch created by the request will be fired by this page)
- `FOR_CUSTOMER` (boolean) [external_data_touch_policy=agent_managed]
- `DEV_LEAD` (enum) [external_data_touch_policy=agent_managed]
- `PM_LEAD` (enum) [external_data_touch_policy=agent_managed]

#### Outgoing Relations

- `submits -> BranchRequest` (`1..*`)


#### Incoming Relations

- `accessesBranchRequestPage <- Person(PM)` (`0..*`)

#### Agent Actions (First Round)

- `fill_and_submit`: agent fills all managed fields by branch-creation rules and submits the request.
- `validate_inputs`: agent verifies required values and option compatibility before submit.
- `capture_submission`: agent captures submit time (from the upcoming email notify) and normalized payload for downstream `BranchRequest`.



### Entity: `BranchRequest`

#### Surfacing Applications

- Internal data object linking `BranchRequestPage`, `EmailNotify`, and `Branch`.

#### Properties

- `requestId` (string, primary key, internal)
- `requestStatus` (enum: submitted/approved/failed/cancelled) (updated by reading `EmailNotify`)
- `requestedAt` (datetime) (captured not from the request page, but from the 'Branch Request' email notify's receive time)
- `branchName` (text) (update by receiving Branch Request notify)
- `majorVersion` (text) (update by receiving Branch Request notify)
- `minorVersion` (text) (update by receiving Branch Request notify)
- `branchPoint` (text) (update by receiving Branch Request notify)
- `branchType` (text) (update by receiving Branch Request notify)
- `top3CaseId` (text) (update by receiving Branch Request notify)
- `forCustomer` (boolean) (update by receiving Branch Request notify)
- `productFamily` (string) (update by receiving Branch Request notify)
- `branchSourceCodeURL` (text nullable) (update by receiving Branch Approved notify)
- `branchJobURL` (text nullable) (update by receiving Branch Approved notify)


#### Outgoing Relations

- `generatesBranch -> Branch` (`0..1`) (key: `branchName`)
- `raisedForTop3Case` -> `Top3Case` (`1..1`)

#### Incoming Relations

- `submits <- BranchRequestPage` (`1..*`)
- `updatesStatus <- EmailNotify` (`0..*`)



### Entity: `EmailNotify`

#### Surfacing Applications

- outlook app
- outlook web mail

#### Properties

- `emailNotifyId` (string, primary key)
- `receivesAt` (datetime)
- `sender` (enum: devops-jenkins-notifs@fortinet.com; jenkins-nonreply@fortinet.com; noreply@mantis.fortinet.com)
- `titleRaw` (text)
- `eventType` (enum: `top3case-bug-note`; `issue-bug-note`; `jenkins-branch-requested`; `jenkins-branch-approved`; `jenkins-branch-rejected`; `jenkins-building-success`; `jenkins-building-failure`; `phab-note`) (agent extracts from `titleRaw`)
- `branchName` (text) (extract from titleRaw when eventType = `jenkins-branch-approved`)
- `branchSourceCodeURL` (text nullable) (agent extracts from the notify email body when eventType = `jenkins-branch-approved`)
- `branchJobURL` (text nullable) (extract from the email body when eventType = `jenkins-branch-approved`)
- `buildNumber` (text nullable) (extract from `titleRaw` when eventType = `jenkins-building-success`)
- `imagesStoreAt` (text nullable) (extract from the titleRaw when  eventType = `jenkins-building-success`) 
- `consoleOutputURL` (text nullable) (extract from email body when eventType = `jenkins-building-failure`)
- `ticketURL` (text) (extract from email body when eventType = `top3case-bug-note`; `issue-bug-note`; `phab-note`)

#### Outgoing Relations

- `updatesStatus -> BranchRequest` (`0..*`, matched by `branchName` + event semantics)

#### Agent Actions (First Round)

- `extract_event`: parse email title/body and classify `eventType`.
- `map_request`: resolve target `BranchRequest` by `branchName` and recency.
- `apply_status_update`: update `BranchRequest.requestStatus` based on normalized event semantics.


### Entity: `Branch`

#### Surfacing Applications

- Branch Request Page  
- jenkins-sjc.corp.fortinet.com/user/<userid>/builds (product family fos existing branches created by pm <userid>)
- jenkins.corp.fortinet.com/user/<userid>/builds (product family other existing branches created by pm <userid>)
- Branch Approved Email Notifies
- Top3 Mantis ticket `branch merge list` field

#### Properties

- `branchName` (string, primary key, normalized from `NEW_BRANCH_NAME`)
- `createdByApp` (enum: `Jenkins`/`Human`/`Unknown`)
- `createdByRef` (string, nullable; source ref such as Jenkins request id/job path or manual operation note id)
- `createdAt` (datetime)
- `lastSyncedAt` (datetime, nullable; last successful cross-app sync verification time)
- `sourceBranchRequestId` (string)

#### Outgoing Relations

- `hasDevLead -> Person(Dev)` (`1..1`)
- `hasPmLead -> Person(PM)` (`1..1`)
- `exposesBuildEntry -> BuildRequestPage` (`1..1`)

#### Incoming Relations

- `usesBranch <- Top3Case` (`0..*`)
- `generatesBranch <- BranchRequest`
- `usesBranch <- BuildRequest`

### Entity: `BuildRequestPage`

#### Properties

- `buildRequestPageId` (string, primary key)
- `pageUrl` (string)
- `jobPath` (string)
- `buttonLabel` (string, default: `Build with Parameters`)

#### Incoming Relations

- `exposesBuildEntry <- Branch`
- `openedFrom <- BuildRequest`

### Entity: `BuildRequest`

#### Properties

- `buildRequestId` (string, primary key)
- `requestedAt` (datetime)
- `requestStatus` (enum: submitted/queued/started/failed/cancelled)
- `selectedCombinations` (list[text]) [external_data_touch_policy=agent_managed]
- `RUN_DB_UPDATE` (boolean) [external_data_touch_policy=agent_managed]
- `APPEND_MODEL_NAME` (boolean) [external_data_touch_policy=agent_managed]
- `TRIAL` (boolean) [external_data_touch_policy=agent_managed]
- `MATURITY` (enum: `F`/`M`) [external_data_touch_policy=agent_managed]
- `SA` (boolean) [external_data_touch_policy=agent_managed]
- `SA_PATCH_VERSION` (text) [external_data_touch_policy=agent_managed]

#### Outgoing Relations

- `openedFrom -> BuildRequestPage`
- `usesBranch -> Branch`
- `spawns -> BuildRun` (`0..*`)

#### Incoming Relations

- `submitsBuildRequest <- Person(PM|Dev)`

### Entity: `BuildRun`

#### Properties

- `buildId` (string, primary key)
- `buildTag` (string)
- `result` (enum: success/failure)
- `triggeredAt` (datetime)
- `notifiedAt` (datetime)
- `notificationRef` (string)

#### Outgoing Relations

- `publishesTop3Build -> Top3Build` (`1..*`)

#### Incoming Relations

- `spawns <- BuildRequest` (`0..*`)
- `validates <- ValidationRecord`

### Entity: `PhabricatorTask`

#### Properties

- `phabTaskId` (string, primary key, e.g. `T28209`)
- `phabUrl` (string)
- `top3CaseId` (string, FK to `Top3Case.caseId`)
- `title` (string)
- `status` (string, current snapshot)
- `assignedToId` (personId, nullable)
- `updatedAt` (datetime, latest visible task activity time)
- `lastCheckedAt` (datetime, agent poll time)
- `lastStatus` (string, nullable, previous snapshot for delta detection)
- `latestCommentAt` (datetime, nullable)
- `latestCommentBy` (personId, nullable)
- `latestCommentSummary` (text, nullable)
- `latestChecklistUrls` (list[string])
- `latestChecklistNames` (list[string])
- `checklistSyncedToTop3At` (datetime, nullable)
- `checklistSyncState` (enum: pending/synced/failed)
- `checklistSyncError` (text, nullable)

#### Outgoing Relations

- `linkedTo -> Top3Case` (`1..1`)
- `assignedTo -> Person` (`0..1`)
- `authoredBy -> Person` (`0..1`)

#### Incoming Relations

- `references <- BugNote` (`0..*`)
- `trackedIn <- ValidationRecord` (`0..*`)

#### Property Source Check (Double-Checked)

- `phabTaskId` <- direct from Phab task ID token (e.g., `T28209`). `CONFIRMED`
- `title` <- direct from Phab task title. `CONFIRMED`
- `status` <- direct from Phab current status. `CONFIRMED`
- `assignedToId` <- direct from Phab assignee account ID. `CONFIRMED`
- `phabUrl` <- composed by PM/Agent as canonical task URL. `CONFIRMED`
- `top3CaseId` <- composed by PM/Agent from Phab-to-Mantis ID mapping context. `CONFIRMED`
- `updatedAt` <- composed by PM/Agent from latest visible timeline event timestamp. `CONFIRMED`
- `lastCheckedAt` <- composed by PM/Agent at each scrape/check run. `CONFIRMED`
- `lastStatus` <- composed by PM/Agent from previous cached snapshot. `CONFIRMED`
- `latestCommentAt` <- composed by PM/Agent from latest comment event timestamp. `CONFIRMED`
- `latestCommentBy` <- composed by PM/Agent from latest comment author account. `CONFIRMED`
- `latestCommentSummary` <- composed by PM/Agent as summarized extraction from latest comment. `CONFIRMED`
- `latestChecklistUrls` <- composed by PM/Agent from parsed checklist attachment links. `CONFIRMED`
- `latestChecklistNames` <- composed by PM/Agent from parsed checklist attachment names. `CONFIRMED`
- `checklistSyncedToTop3At` <- composed by PM/Agent sync timestamp to Mantis Top3. `CONFIRMED`
- `checklistSyncState` <- composed by PM/Agent sync workflow state. `CONFIRMED`
- `checklistSyncError` <- composed by PM/Agent error text from failed sync. `CONFIRMED`

#### Relation Source Check (Double-Checked)

- `linkedTo Top3Case` <- PM/Agent mapping using Mantis ID context from Phab task. `CONFIRMED`
- `assignedTo Person` <- direct from Phab assignee field. `CONFIRMED`
- `authoredBy Person` <- direct from Phab task author field. `CONFIRMED`
- `references <- BugNote` <- parsed `phabTaskIds` extraction and cross-linking workflow. `CONFIRMED`

### Entity: `ValidationRecord`

#### Properties

- `validationId` (string, primary key)
- `environment` (string)
- `reproSteps` (text)
- `observedResult` (text)
- `expectedResult` (text)
- `validationStatus` (enum)
- `evidenceLink` (string)

#### Outgoing Relations

- `validates -> BuildRun`
- `validatesIssue -> Issue`
- `trackedIn -> PhabricatorTask`

#### Incoming Relations

- `summarizes <- ReleaseNote`

### Entity: `ReleaseNote`

#### Properties

- `releaseNoteId` (string, primary key)
- `content` (text)
- `author` (personId)
- `createdAt` (datetime)

#### Outgoing Relations

- `summarizes -> ValidationRecord`

#### Incoming Relations

- `publishesDeliverable <- Top3Case` (`0..*`)

### Entity: `Person`

#### Properties

- `personId` (string, primary key)
- `name` (string)
- `roleType` (enum: PM/Dev/QA/Support)
- `createsBranchNames` (list[string], nullable; inferred from branch creation traces, to be finalized in later round)

#### Incoming Relations

- `ownedBy <- Top3Case`
- `hasQaOwner <- Top3Case`
- `hasDevOwner <- Top3Case`
- `hasSupportOwner <- Top3Case`
- `hasDevLead <- Branch`
- `hasPmLead <- Branch`
- `accessesBranchRequestPage <- Person(PM)`
- `submitsBuildRequest <- Person(PM|Dev)`

### Entity: `Team`

#### Properties

- `teamId` (string, primary key)
- `teamType` (enum: Core/All)
- `channelRef` (string)

#### Outgoing Relations

- `usesThread -> CommunicationThread`

#### Incoming Relations

- `hasTeam <- Top3Case`

### Entity: `CommunicationThread`

#### Properties

- `threadId` (string, primary key)
- `platform` (enum: Teams)
- `topic` (string)
- `createdAt` (datetime)

#### Incoming Relations

- `usesThread <- Team`

### 2.1 Common Identity and Link Keys (Global)

1. `Top3Case.caseId` = TOP3 `Mantis bug_id`.
2. `Top3Case.mantisUrl` is canonical case URL.
3. Owner IDs on `Top3Case` (`pmOwnerId`/`qaOwnerId`/`devOwnerId`/`supportOwnerIds`) are denormalized snapshots; owner relations are canonical.
4. `NFR.nfrId` = NFR `Mantis bug_id`.
5. `NFR.product` and `NFR.solutionRaw` mirror Mantis `Product` and `Solution` fields directly.
6. `NFR.solutionTags` is a normalized, comma-split representation of `NFR.solutionRaw`.
7. `NFR.conclusion` is AI-agent inferred/composed from full NFR ticket evidence and stored as synthesized NFR conclusion narrative.
8. `NFR.duplicateNfrId` maps from Mantis `Duplicate ID`; `NFR.duplicateOf -> NFR` resolves when value is present.
9. `NFR.reporterId` is a denormalized snapshot; `NFR.reportedBy -> Person` is canonical.
10. `NFR.referredIssueIds` and `NFR.referredNfrIds` are agent-extracted from `NFR.hasBugNote -> BugNote.rawText` and `NFR.description`.
11. `Issue.issueId` = bug-fix `Mantis bug_id`.
12. `BugNote.bugNoteId` maps to Mantis bugnote ID when available.
13. `BugNote.mentionedMantisIds` stores all `#<mantisId>` mentions.
14. `BugNote.mentionedTicketIds` are parsed Mantis cross-record references.
15. `BugNote.phabTaskIds` are resolved from Phabricator search using `Top3Case.caseId` + QA identity context.
16. `BugNote.bugNoteEditUrl`/`bugNoteDeleteUrl` are agent shortcut URLs derived from ticket and bugnote ids.
17. `Person.personId` should use unique company account id (e.g., `sjeff`) when mapping `BugNote authoredBy Person`.
18. `PhabricatorTask.phabTaskId` is the canonical cross-system key (e.g., `T28209`).
19. `PhabricatorTask.top3CaseId` links to `Top3Case.caseId` via Phab `Mantis IDs` mapping.
20. `Top3Case.currentPhase` may be derived from `BugNote.headlineType` plus note content cues, with provenance in `currentPhaseSource/currentPhaseEvidenceNoteId`.
21. Release summary content is captured in `BugNote(headlineType=release)`; no standalone Top3Case release-summary property.
22. `Top3Case.closureReason` is AI-agent inferred/composed from final state and close/release bugnote evidence.
23. `ActionHistory` is a case-level container of the Mantis `Action History` section and anchors grouped build records.
24. `Top3Build.top3BuildId` is deterministic by `caseId + buildNumber`.
25. `Patch.patchId` is deterministic by `top3BuildId + issueId`.
26. `Patch.mainBranchPointRaw` is extracted from `fixesIssue -> Issue.resolvedIn` by tokenization rule; `Patch.mainBranchPoint` is numeric parse when available.
27. `Patch.mergeToMainState` is derived state for quick agent/human filtering of main-branch merge readiness.
28. `Top3Case.branch` is a denormalized snapshot from Mantis `Branch Merge List`; canonical case-branch linkage is `Top3Case.usesBranch -> Branch`.
29. `BranchRequest.branchName` normalizes to `Branch.branchName` when request succeeds.
30. `Branch.branchName` links Mantis/Phab/Jenkins contexts.
31. `Branch.createdByApp` and `Branch.createdByRef` capture origin provenance (Jenkins create-branch request preferred; manual fallback allowed).
32. `BranchRequestPage.PM_LEAD` / `BranchRequestPage.DEV_LEAD` map to `Person.personId`.
33. `EmailNotify.eventType` is derived from email title/body and drives branch-request status sync.
34. `EmailNotify.branchName` is the join key to `BranchRequest.branchName` for status updates.
35. `BuildRequestPage.pageUrl` is branch-page navigable endpoint.
36. `BuildRun.buildId/buildTag` links `Jenkins -> Outlook -> InfoSite`.
37. `Top3Build.buildNumber` is the operational retrieval key for build artifacts in InfoSite context.
38. `ValidationRecord.evidenceLink` links checklist/log evidence in Phabricator.
39. `Team.channelRef` and `CommunicationThread.threadId` identify Core/All channels.
40. `Issue.hasBugNote -> BugNote` is the canonical issue-note linkage keyed by issue `bug_id` and bugnote IDs.
41. `Issue.referredIssueIds` are agent-extracted issue IDs from linked issue bugnote text (`Issue.hasBugNote -> BugNote.rawText`) using `#id`, `mantis#id`, `bug_id=id`.
42. `Issue.referredNfrIds` are agent-extracted NFR IDs from linked issue bugnote text (`Issue.hasBugNote -> BugNote.rawText`) using `#id`, `mantis#id`, `bug_id=id`.
43. `Issue.references -> Issue` resolves from `Issue.referredIssueIds` after ticket-type filtering.
44. `Issue.references -> NFR` resolves from `Issue.referredNfrIds` after ticket-type filtering.
45. `Issue.duplicateIssueId` maps from Mantis `Duplicate ID`; `Issue.duplicateOf -> Issue` resolves when value is present.
46. `Issue.assignedDevId` maps from Mantis `Assigned To` and normalizes to `Person.personId` account token.
47. `Issue.qaAssigneeId` maps from Mantis `QA Assignee` and normalizes to `Person.personId` account token.
48. `Issue.fixSchedule` and `Issue.resolvedIn` map directly from same-name Mantis fields.
49. `Issue.category`, `Issue.severity`, `Issue.reproducibility`, `Issue.reporterId`, `Issue.keyword`, `Issue.reproducedById`, `Issue.reportedVersion`, and `Issue.earliestBuildNumber` map directly from same-name Mantis issue fields (`Reporter`/`Reproduced By` normalize to `Person.personId`).
50. `Issue.conclusion` is AI-agent inferred/composed from full issue-ticket evidence and stored as synthesized closure narrative.
51. `Issue.assignedDevId`/`Issue.qaAssigneeId` are denormalized snapshots; `Issue.fixedBy`/`Issue.hasQaAssignee` relations are canonical for graph traversal.
52. `PhabricatorTask.assignedToId` is denormalized snapshot; `PhabricatorTask.assignedTo` relation is canonical.

## 3. Derivation / Composition Rules

| Target Field                          | Rule                                                                   | Inputs                                                                                               | Agent Responsibility                                           | Confirmation                 |
| ------------------------------------- | ---------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- | ---------------------------- |
| `Top3Case.currentPhase`               | derive lifecycle phase from structured/unstructured case evidence      | `BugNote.headlineType`, note content cues                                                            | derive and persist phase state                                 | `HPO-CONFIRMED DESIGN FIELD` |
| `Top3Case.currentPhaseSource`         | provenance marker for phase derivation/manual override                 | latest phase update method                                                                           | persist provenance for auditability                            | `DESIGN FIELD`               |
| `Top3Case.currentPhaseEvidenceNoteId` | evidence pointer for current phase                                     | source `bugNoteId` used in derivation                                                                | record evidence pointer                                        | `DESIGN FIELD`               |
| `Top3Case.closedAt`                   | derive closure timestamp from close/release note                       | `BugNote.createdAt` where headline is `---Release---`/`---Close---`                                  | derive and set closure time                                    | `HPO-CONFIRMED RULE`         |
| `Top3Case.closureReason`              | infer and compose normalized closure rationale                         | final state + release/close bugnote evidence                                                         | infer and compose closure reason                               | `HPO-CONFIRMED RULE`         |
| `NFR.solutionTags`                    | normalize solution tags from raw Mantis solution string               | `NFR.solutionRaw`                                                                                     | split by comma, trim, dedupe, and persist normalized tags      | `HPO-CONFIRMED RULE`         |
| `NFR.referredIssueIds`                | extract referenced Issue IDs from NFR-local text                      | `NFR.hasBugNote -> BugNote.rawText`, `NFR.description`                                               | parse, dedupe, type-filter (Issue), and persist                | `HPO-CONFIRMED RULE`         |
| `NFR.referredNfrIds`                  | extract referenced NFR IDs from NFR-local text                        | `NFR.hasBugNote -> BugNote.rawText`, `NFR.description`                                               | parse, dedupe, type-filter (NFR), and persist                  | `HPO-CONFIRMED RULE`         |
| `NFR.conclusion`                      | infer and compose NFR-level conclusion from full NFR ticket review     | `NFR.summary`, `NFR.description`, `NFR.additionalInformation`, `NFR.status`                           | synthesize concise NFR conclusion for PM/QA consumption        | `HPO-CONFIRMED RULE`         |
| `Issue.referredIssueIds`              | extract referenced issue IDs from linked issue bug notes               | `Issue.hasBugNote -> BugNote.rawText` (`#id`, `mantis#id`, `bug_id=id`)                              | parse, dedupe, and persist linked issue IDs                    | `HPO-CONFIRMED RULE`         |
| `Issue.referredNfrIds`                | extract referenced NFR IDs from linked issue bug notes                | `Issue.hasBugNote -> BugNote.rawText` (`#id`, `mantis#id`, `bug_id=id`)                              | parse, dedupe, type-filter (NFR), and persist linked IDs       | `HPO-CONFIRMED RULE`         |
| `Issue.conclusion`                    | infer and compose issue-level conclusion from full issue ticket review | `Issue.title`, `Issue.description`, `Issue.hasBugNote -> BugNote.rawText`, `Issue.status`, `Issue.resolvedIn`, assignment/repro metadata | synthesize concise conclusion for downstream PM/QA consumption | `HPO-CONFIRMED RULE`         |
| `Patch.mainBranchPointRaw`            | extract candidate main-branchpoint token from linked issue resolution field | `Patch.fixesIssue -> Issue.resolvedIn`                                                                    | tokenize/normalize `resolvedIn`; keep first branchpoint-like token | `HPO-CONFIRMED RULE`         |
| `Patch.mainBranchPoint`               | derive numeric main-branchpoint from extracted raw token               | `Patch.mainBranchPointRaw`                                                                               | parse integer branchpoint or null                               | `HPO-CONFIRMED RULE`         |
| `Patch.mergeToMainState`              | derive merge state for agent triage                                    | `Patch.mainBranchPointRaw`, `Patch.mainBranchPoint`                                                      | map to merged/not-merged/unknown state                          | `HPO-CONFIRMED RULE`         |
| `riskScore`                           | compute risk score for case progression                                | blockers, missing links, status aging, severity                                                      | calculate and refresh score                                    | `DESIGN FIELD`               |
| `blockerFlags`                        | identify active blockers requiring escalation                          | dependency gaps, failed builds, stalled validation                                                   | detect and emit blocker flags                                  | `DESIGN FIELD`               |
| `missingLinks`                        | detect missing traceability links                                      | ticket/branch/build/top3build/validation mappings                                                    | compute missing link list                                      | `DESIGN FIELD`               |
| `nextBestActions`                     | recommend next executable actions                                      | current bottleneck + role ownership + SLA heuristics                                                 | generate ranked next actions                                   | `DESIGN FIELD`               |

## 4. Open Confirmations

| Item                                                  | Why Open                                         | Proposed Resolution                             | Owner       |
| ----------------------------------------------------- | ------------------------------------------------ | ----------------------------------------------- | ----------- |
| `BranchRequestPage` property/relation source checks   | source-check sections not yet documented in v3.1 | perform property + relation source-check review | Peter + HPO |
| `BranchRequest` property/relation source checks       | source-check sections not yet documented in v3.1 | perform property + relation source-check review | Peter + HPO |
| `EmailNotify` property/relation source checks         | source-check sections not yet documented in v3.1 | perform property + relation source-check review | Peter + HPO |
| `Branch` property/relation source checks              | source-check sections not yet documented in v3.1 | perform property + relation source-check review | Peter + HPO |
| `BuildRequestPage` property/relation source checks    | source-check sections not yet documented in v3.1 | perform property + relation source-check review | Peter + HPO |
| `BuildRequest` property/relation source checks        | source-check sections not yet documented in v3.1 | perform property + relation source-check review | Peter + HPO |
| `BuildRun` property/relation source checks            | source-check sections not yet documented in v3.1 | perform property + relation source-check review | Peter + HPO |
| `ValidationRecord` property/relation source checks    | source-check sections not yet documented in v3.1 | perform property + relation source-check review | Peter + HPO |
| `ReleaseNote` property/relation source checks         | source-check sections not yet documented in v3.1 | perform property + relation source-check review | Peter + HPO |
| `Person` property/relation source checks              | source-check sections not yet documented in v3.1 | perform property + relation source-check review | Peter + HPO |
| `Team` property/relation source checks                | source-check sections not yet documented in v3.1 | perform property + relation source-check review | Peter + HPO |
| `CommunicationThread` property/relation source checks | source-check sections not yet documented in v3.1 | perform property + relation source-check review | Peter + HPO |

## 5. Decision Log

- [2026-02-23 10:07:03 PST] `Top3Case.closureReason` confirmed as AI-agent inferred/composed (`v1.2`).
- [2026-02-23 10:13:00 PST] `BugNote` relation source-check block completed (`v1.3`).
- [2026-02-23 10:52:52 PST] Discovery guidance updated to require explicit as-is and agent-oriented examples.
- [2026-02-23 10:52:52 PST] `v1.4` aligns data model documentation to lifecycle-centric discovery template.
- [2026-02-23 11:42:14 PST] `Issue` property/relation source-check completed and aligned to discovery template (`v1.5`).
- [2026-02-23 12:07:16 PST] Reopened `Issue` review and completed `v1.6` update: added `referredIssueIds`, `duplicateIssueId`, `assignedDevId`, `qaAssigneeId`, `fixSchedule`, `resolvedIn`, plus Issue->Issue/Person relations and derivation rule for issue bugnote reference extraction.
- [2026-02-23 13:06:34 PST] Reopened `Issue` review and completed `v1.7` update: added Mantis-mirrored fields (`category`, `severity`, `reproducibility`, `reporterId`, `keyword`, `reproducedById`, `reportedVersion`, `earliestBuildNumber`) and added agent-composed `Issue.conclusion` with derivation/source-check rules.
- [2026-02-23 13:17:09 PST] Reopened `Issue` review and completed `v1.8` normalization: standardized bug-note modeling to relation form by removing `Issue.bugNotes` and adding `Issue.hasBugNote -> BugNote`; updated derivation inputs to linked `BugNote.rawText`.
- [2026-02-23 13:24:48 PST] Cross-entity normalization audit (`v1.9`): fixed missing `Top3Case.devOwnerId` declaration in markdown entity card and clarified canonical rule that owner/assignee ID snapshot fields are denormalized while relations remain canonical.
- [2026-02-23 15:37:57 PST] `NFR` entity review completed in `v1.10` from Mantis ticket `1225001`: baseline fields confirmed with added `product` and `solution`, and property/relation source-check sections documented.
- [2026-02-23 15:46:15 PST] Versioning policy adjustment: advanced active model line to `v2.0` and will avoid decimal increments beyond `.9` in subsequent versions.
- [2026-02-23 15:49:59 PST] `NFR` enhancement in `v2.1`: added `NFR.conclusion` as AI-agent inferred/composed field, aligned with Issue conclusion pattern and derivation governance.
- [2026-02-23 19:10:15 PST] `ActionHistory` enhancement in `v2.2`: remodeled from generic event log to grouped build-merge mapping (`Top3Build` context + fix rows), added source-checked fields (`buildNumber`, `mantisId`, `mergeTargetRaw`, `summary`) and derivation rules for `mergedToTrunk`/`trunkBranchPoint`.
- [2026-02-23 19:24:21 PST] `ActionHistory` simplification in `v2.3`: extracted `Top3Build` and `Patch` entities; retained ActionHistory as section container; moved merge/title/issue mapping to `Patch` (`issueId`, `mainBranchPoint`, `issueTitle`) and added `Top3Build hasPatch -> Patch` + `Patch fixesIssue -> Issue`.
- [2026-02-23 22:02:11 PST] `Patch` source refinement in `v2.4`: set `Patch.mainBranchPointRaw` to derive from linked `Issue.resolvedIn` token extraction and `Patch.issueTitle` to source from linked `Issue.title` via `fixesIssue -> Issue`.
- [2026-02-24 20:39:12 PST] `Branch` enhancement in `v2.5`: added origin/sync provenance fields (`createdByApp`, `createdByRef`, `lastSyncedAt`) and added canonical `Top3Case usesBranch -> Branch` relation while keeping `Top3Case.branch` as denormalized snapshot.
- [2026-02-24 21:32:20 PST] `v2.6` entity merge: unified `Artifact` into `Top3Build`; replaced `BuildRun publishesArtifact -> Artifact` with `BuildRun publishesTop3Build -> Top3Build`, and removed standalone `Artifact` entity.
- [2026-02-25 08:42:10 PST] `NFR` enhancement in `v2.7`: normalized NFR schema (`solutionRaw/solutionTags`, `duplicateNfrId`, `referredNfrIds`), added canonical NFR relations (`reportedBy`, `hasBugNote`, `references`, `duplicateOf`), and added explicit agent action policy for ingest/sync/inference/writeback.
- [2026-02-25 08:25:40 PST] `Patch` enhancement in `v2.8`: added normalized merge state field (`mergeToMainState`), refined Patch source/derivation mapping, and added Patch-level agent action policy for ingest/sync/derive workflow.
- [2026-02-25 08:39:45 PST] Version promotion to `v2.9`: carried forward HPO edits from `v2.8` and synchronized canonical model pointers across markdown/YAML/Mermaid artifacts.
- [2026-02-26 09:18:30 PST] `v3.0` normalization: consolidated HPO `v2.9.1` updates (Surfacing Applications sections, `BranchRequestPage`, `EmailNotify`) and aligned naming/cardinality/agent-action conventions for these entities.
- [2026-02-26 09:46:12 PST] `v3.1` strict check: finalized overlap rules between `BranchRequestPage` and `BranchRequest`, standardized request/email synchronization semantics, and recorded strict-check outcomes in dedicated section.
