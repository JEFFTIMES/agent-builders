# ActionHistory Field Candidates - Mantis 1229978 (v0.1)

Source ticket: `working/agent-top3pm/assets/top3apps/1229978 -- Mantis.html`
Source section: `Bug History` table (`Date Modified`, `Username`, `Field`, `Change`)

## 1. Observed raw columns in Mantis Bug History
- `Date Modified` (e.g., `2026-02-11 10:25:12`)
- `Username` (display + account token, e.g., `Jeff Song (sjeff)`)
- `Field` (event descriptor, e.g., `Status`, `Bugnote Added: 7588672`, `File Added: qa-checklist-8956.txt`, `Description Updated`, `New Bug`)
- `Change` (optional, often `from => to`; blank for add/update events)

## 2. Recommended ActionHistory entity fields (for model)
- `actionId` (string, primary key; agent-composed deterministic id per row)
- `caseId` (string, parent Mantis bug_id, e.g., `1229978`)
- `actionAt` (datetime, from `Date Modified`)
- `actorId` (personId, normalized from username account token in parentheses)
- `actionType` (enum, agent-classified from `Field`)
- `actionField` (string, nullable; field name for field-change events, e.g., `Status`, `ETA`, `Assigned To`)
- `fromValue` (string, nullable; parsed from `Change` left side when pattern `A => B` exists)
- `toValue` (string, nullable; parsed from `Change` right side when pattern `A => B` exists)
- `note` (text, nullable; compact synthesized note for downstream use)
- `sourceSystem` (enum; here `Mantis`)
- `rawField` (text; exact `Field` cell value)
- `rawChange` (text, nullable; exact `Change` cell value)
- `rawRef` (string, nullable; extracted anchor like bugnoteId/fileName when present)

## 3. Suggested `actionType` enum
- `new_bug`
- `field_changed`
- `description_updated`
- `bugnote_added`
- `bugnote_edited`
- `bugnote_deleted`
- `file_added`
- `monitor_added`
- `other`

## 4. Sample mappings from real rows
1. `2025-11-24 13:37:51 | Jenny Shao (jennyshao) | Status | New => Investigating`
- `actionType=field_changed`, `actionField=Status`, `fromValue=New`, `toValue=Investigating`

2. `2026-01-23 16:38:35 | Jeff Song (sjeff) | File Added: qa-checklist-8956.txt | (blank)`
- `actionType=file_added`, `rawRef=qa-checklist-8956.txt`

3. `2026-01-23 16:39:05 | Jeff Song (sjeff) | Bugnote Added: 7588672 | (blank)`
- `actionType=bugnote_added`, `rawRef=7588672`

4. `2026-02-05 15:34:12 | Jeff Song (sjeff) | ETA | 2026-01-23 => 2026-01-28`
- `actionType=field_changed`, `actionField=ETA`, `fromValue=2026-01-23`, `toValue=2026-01-28`

## 5. Open confirmation points for HPO
- Keep `fromValue`/`toValue` split fields, or only keep `rawChange`?
- Keep both `actionField` and `rawField`?
- Keep `note` as AI-agent composed summary?
- Keep monitor events (`Bug Monitored: ...`) in ActionHistory, or exclude as noise?
