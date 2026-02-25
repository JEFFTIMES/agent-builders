# ActionHistory Field Candidates - Mantis 1229978 (v0.3)

Source ticket: `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html`
Source section: `<!-- Action History-->` block (table columns: `ID`, `Merged`, `Summary`)

## 1. What this section actually contains
The Action History table is a grouped merge-trace view, not a changelog timeline.
Observed row patterns:
- `Top3Build` marker row (e.g., `8928`, `8956 (for 3800G/3801G)`, `8997 (for 3800G/3801G)`)
- merged ticket row with:
  - `ID`: linked ticket id (e.g., `1225001`, `1241381`, `1241309`)
  - `Merged`: source system (`Mantis` in current sample)
  - `Summary`: merged item summary text
- trailing blank spacer row

## 2. Corrected ActionHistory entity candidate (for model)
- `actionHistoryId` (string, primary key; deterministic key per row)
- `caseId` (string; parent Top3 case id, e.g., `1229978`)
- `sequenceNo` (integer; row order in Action History table)
- `rowType` (enum: `build_marker` / `fix_entry` / `spacer`)
- `buildNumber` (string; inherited context from latest `Top3Build` row)
- `buildQualifier` (text, nullable; build suffix like `for 3800G/3801G`)
- `mantisId` (string, nullable; fix ticket id when `rowType=fix_entry`)
- `mergeTargetRaw` (string, nullable; value in `Merged` column, e.g., `1978`, `2887`, `Mantis`)
- `mergedToTrunk` (boolean, derived; true when `mergeTargetRaw` is numeric)
- `trunkBranchPoint` (integer, nullable, derived from `mergeTargetRaw` when numeric)
- `summary` (text, nullable; summary text shown in table)
- `sourceSystem` (enum; `Mantis`)
- `sourceSection` (string; `Action History`)
- `sourceTicketId` (string; parent page bug_id)
- `rawIdCell` (text)
- `rawMergedCell` (text)
- `rawSummaryCell` (text)

## 3. Recommended relation interpretation
- `Top3Case hasActionHistory -> ActionHistory` (existing)
- `ActionHistory references -> NFR|Issue` when `mantisId` resolves to ticket type by lookup

## 4. Sample mapping from the provided section
1. `Top3Build | 8928`
- `rowType=build_marker`, `buildNumber=8928`, `buildQualifier=null`

2. `1225001 | Mantis | (RADIUS enriched traffic log...)`
- `rowType=fix_entry`, `mantisId=1225001`, `mergeTargetRaw=Mantis`, `mergedToTrunk=false`, `trunkBranchPoint=null`, `buildNumber=8928`

3. `Top3Build | 8956 (for 3800G/3801G)`
- `rowType=build_marker`, `buildNumber=8956`, `buildQualifier=for 3800G/3801G`

4. `1241309 | Mantis | (fg_7-4_logging... one)`
- `rowType=fix_entry`, `mantisId=1241309`, `mergeTargetRaw=Mantis`, `mergedToTrunk=false`, `trunkBranchPoint=null`, `buildNumber=8956`

## 5. Confirmation needed from HPO
- Keep spacer rows as records (`itemType=spacer`) or ignore them?
- Keep `raw*` fields for traceability/debug, or keep only normalized fields?
- Should `buildNumber` be modeled as string or integer?
- Do we keep `buildQualifier` as separate field (e.g., `for 3800G/3801G`), or append it into `buildNumber` text?
