# ActionHistory Field Candidates - Mantis 1229978 (v0.2)

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
- `actionHistoryId` (string, primary key; AI-agent composed deterministic key per row)
- `caseId` (string; parent Top3 case id, e.g., `1229978`)
- `top3BuildTag` (string; current build context inherited from latest `Top3Build` row)
- `itemType` (enum: `build_marker` / `merged_ticket` / `spacer`)
- `itemTicketId` (string, nullable; merged ticket id when `itemType=merged_ticket`)
- `mergedFrom` (string, nullable; source in `Merged` column, e.g., `Mantis`)
- `itemSummary` (text, nullable; summary text shown in table)
- `buildNote` (text, nullable; suffix note from build marker, e.g., `for 3800G/3801G`)
- `sequenceNo` (integer; row order in Action History table)
- `sourceSystem` (enum; `Mantis`)
- `rawIdCell` (text)
- `rawMergedCell` (text)
- `rawSummaryCell` (text)
- `rawRef` (string, nullable; URL or referenced ticket id)

## 3. Recommended relation interpretation
- `Top3Case hasActionHistory -> ActionHistory` (existing)
- `ActionHistory references -> NFR|Issue` when `itemTicketId` resolves to ticket type by lookup
- `ActionHistory references -> BuildRun` when `top3BuildTag` maps to build tag/build id in model

## 4. Sample mapping from the provided section
1. `Top3Build | 8928`
- `itemType=build_marker`, `top3BuildTag=8928`, `buildNote=null`

2. `1225001 | Mantis | (RADIUS enriched traffic log...)`
- `itemType=merged_ticket`, `itemTicketId=1225001`, `mergedFrom=Mantis`, `top3BuildTag=8928`

3. `Top3Build | 8956 (for 3800G/3801G)`
- `itemType=build_marker`, `top3BuildTag=8956`, `buildNote=for 3800G/3801G`

4. `1241309 | Mantis | (fg_7-4_logging... one)`
- `itemType=merged_ticket`, `itemTicketId=1241309`, `mergedFrom=Mantis`, `top3BuildTag=8956`

## 5. Confirmation needed from HPO
- Keep spacer rows as records (`itemType=spacer`) or ignore them?
- Keep `raw*` fields for traceability/debug, or keep only normalized fields?
- Should `top3BuildTag` be modeled as string or integer?
- Do we keep `buildNote` as separate field (e.g., `for 3800G/3801G`), or append it into `top3BuildTag` text?
