## ID <0001>

Date and Time:

User:

Your Question:

User's Answer:

Further Discussion Summary:

Raised New Questions:

## ID <0001>

Date and Time:
2026-02-18 08:22:23 PST

User:
Jeff (TOP3 PM)

Your Question:
Please specify the exact Mantis page/fields/rules to identify and enter an assigned TOP3 case.

User's Answer:
1.1 Open `https://mantis.fortinet.com/view_all_bug_page.php?view_type=0`.
1.2 Choose option `Top-3` from the option list at the right corner on the second row (if not already selected).
1.3 Click `View Bugs` on the 3rd row to refresh.
1.4 Set `Assigned PM` to `Jeff Song (sjeff)` and `Status` to `Bumped Out`, `Investigating`, `Fix Needed`, `Solution Sent` on the 4th row. Then press `Apply`.
1.5 Check TOP3 ID in the second column; scroll if needed; locate target case ID (example `1229978`) and enter the case.
1.5.b Shortcut: open `https://mantis.fortinet.com/bug_view_page.php?bug_id={mantis id}` directly if the TOP3 ID is known.

Further Discussion Summary:
Captured and frozen as Step 1 (`Enter the case`) before moving to later workflow steps.

Raised New Questions:
None.

## ID <0002>

Date and Time:
2026-02-18 08:25:38 PST

User:
Jeff (TOP3 PM)

Your Question:
(Interview scope alignment)

User's Answer:
The current use case describes one kind of TOP3 case (`New Feature Request`). After completing this one, we need to go through another type (`Bug Fixing`).

Further Discussion Summary:
Accepted. Interview evidence and workflow/use-case docs must separate these two variants and process them sequentially.

Raised New Questions:
None.

## ID <0003>

Date and Time:
2026-02-18 08:41:30 PST

User:
Jeff (TOP3 PM)

Your Question:
Which fields are Mandatory / Conditional / Optional for UC-001, and how should field interpretation be documented for cross-system usage?

User's Answer:
Mandatory-first focus includes: `Category`, all ownership/status fields, `Earliest Build Number`, `Description`, `Branch Merge List`, `Hardware Model`, `Support/Field Contact`, `NFR`, `Attached Files`, `Affected Customer`, `PMDB ID`, `Action History`, `Bugnote`, and `Bug Notes`.

Clarifications provided:
- `Category` indicates product line this case works for.
- `Assigned To` / `QA Assignee` identify core execution owners and are used to build Teams Core and filter Phabricator/Jenkins/Outlook/Teams threads.
- `ETA` can be blank early; hard date implies committed customer/business expectation.
- `Earliest Build Number` is oldest version where issue occurs.
- `Description` is the most important context: requirement background, linked NFR/URL, branchpoint, expected timeline, bundled bugs.
- `Branch Merge List` encodes created branches and fork points (`<branch>` + fork point); PM owns and must double-confirm branchpoint with requester before filling.
- `Hardware Model` drives image model selection in Jenkins build triggering.
- `Support/Field Contact` drives Teams All group and communication filtering.
- `NFR` is requested new feature Mantis ID.
- `Attached Files` are supplementary diagnosis/feature docs.
- `Affected Customer` indicates impacted customer (for NFR: feature gap).
- `PMDB ID` is required for NFR approval thread; missing value should remind PM to file PMDB.
- `Action History`: `ID`=bug ID, `Merged`=build number, `Summary`=bug summary.
- `Bugnote` is the text editor to post notes.
- `Bug Notes` store stakeholder communication, milestones, and PM journals (investigation/repro findings, action plan, root cause explanations, case summaries).

Further Discussion Summary:
Dictionary was updated to include classification, implicit linked systems, cross-system usage, and interpreting rule columns.

Raised New Questions:
Pending: confirm final `Conditional` vs `Optional` split for non-core UI/action fields.

## ID <0004>

Date and Time:
2026-02-18 11:10:22 PST

User:
Jeff (TOP3 PM)

Your Question:
For succinct workflow Step 2, what details are still needed: read order, linked bug inclusion rule, NFR validation/PMDB mapping, exit criteria, and step output?

User's Answer:
1. Read order: follow natural sequence; focus on recent bug notes.
2. Other bug-fix requests rule: open each bug Mantis URL in new tab and review with same TOP3 review rule. Keep this general rule for now; deep dive later in use case 2. Assume requested bugs are in scope initially, then ask QA to reproduce on `Earliest Build Number`; if reproducible, confirm in scope.
3. PMDB linkage: sometimes PMDB ID exists in linked NFR; fill that PMDB ID into TOP3 `PMDB ID` field.
4. Exit criteria: nothing additional.
5. Output: after reviewing TOP3 + linked Mantis + PMDB info, PM drafts a mandatory case profile, checks must-have items (topology, configurations, debug logs), and writes a bug note summarizing available information and requesting missing parts from supporters.

Further Discussion Summary:
Step 2 workflow details were documented in `exist_workflow.md`. Added reminder in `sparks.md` to deep dive bug-fix processing rule under UC-002 and corresponding later workflow steps.

Raised New Questions:
None.
