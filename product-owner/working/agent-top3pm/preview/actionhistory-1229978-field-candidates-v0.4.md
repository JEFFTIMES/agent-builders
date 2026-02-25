# ActionHistory Field Candidates - Mantis 1229978 (v0.4)

Source ticket: `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html`
Source section: `<!-- Action History-->` block (table columns: `ID`, `Merged`, `Summary`)

## 1. Confirmed structure interpretation
The section represents grouped data:
- one `Top3Build` group starts at each `Top3Build` row
- following rows are patch entries until the next `Top3Build` row
- each patch row carries:
  - `issueId` from `ID`
  - `mainBranchPoint` from `Merged` when numeric (otherwise not merged marker)
  - `issueTitle` from `Summary`

## 2. Simplified entity proposal

### `ActionHistory` (container)
- `actionHistoryId` (string, PK)
- `caseId` (string)
- `sourceSystem` (enum: `Mantis`)
- `sourceSection` (string: `Action History`)

Relations:
- `Top3Case hasActionHistory -> ActionHistory` (`0..*`)
- `ActionHistory hasTop3Build -> Top3Build` (`0..*`)

### `Top3Build`
- `top3BuildId` (string, PK)
- `caseId` (string)
- `buildNumber` (string)
- `buildQualifier` (text, nullable)
- `sequenceNo` (integer)

Relations:
- `Top3Build hasPatch -> Patch` (`0..*`)

### `Patch`
- `patchId` (string, PK)
- `top3BuildId` (string)
- `issueId` (string)
- `mainBranchPointRaw` (string)
- `mainBranchPoint` (integer, nullable, derived)
- `issueTitle` (text)
- `sequenceNo` (integer)

Relations:
- `Patch fixesIssue -> Issue` (`1..1`)

## 3. Why keep `Patch` entity (recommended)
If we use only `Issue` properties, we lose per-build patch context. `mainBranchPoint` and row-level title snapshot belong to the build-patch record, not globally to `Issue`.

## 4. Sample mapping
- `Top3Build | 8956 (for 3800G/3801G)` -> `Top3Build(buildNumber=8956, buildQualifier='for 3800G/3801G')`
- `1241309 | 2887 | (fg_7-4_logging...)` -> `Patch(issueId=1241309, mainBranchPointRaw='2887', mainBranchPoint=2887, issueTitle='fg_7-4_logging...')`
- `1225001 | Mantis | (RADIUS enriched...)` -> `Patch(issueId=1225001, mainBranchPointRaw='Mantis', mainBranchPoint=null, issueTitle='RADIUS enriched...')`

## 5. Remaining confirmation
- `buildNumber` type: keep `string` (recommended) or force integer?
- keep `issueTitle` as snapshot from Action History (recommended) even if Issue title later changes?
