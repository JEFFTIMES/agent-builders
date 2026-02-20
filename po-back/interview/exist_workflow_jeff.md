# Existing Workflow (As-Is)

## Step 1 - Enter the TOP3 Case (Documented)

### 1. Standard Path (Filter then open case)
1. Open Mantis list page: `https://mantis.fortinet.com/view_all_bug_page.php?view_type=0`.
2. Ensure the view preset is `Top-3` from the option list on the second row.
3. Click `View Bugs` to refresh list with the selected preset.
4. Set filters:
   - `Assigned PM` = `Jeff Song (sjeff)`
   - `Status` in {`Bumped Out`, `Investigating`, `Fix Needed`, `Solution Sent`}
5. Click `Apply`.
6. Find target TOP3 ticket ID in the second column (example: `1229978`) and open the case.

### 2. Shortcut Path (Known case ID)
- If TOP3 case ID is already known, open directly:
  - `https://mantis.fortinet.com/bug_view_page.php?bug_id={mantis id}`

### 3. Notes
- This section documents only "Enter the case" per interviewee instruction.
- Remaining workflow steps will be appended after further interview details are captured.

## Step 2 - Review TOP3 Ticket and Linked Requests (Documented)

### 1. Read Sequence
1. Follow the natural top-to-bottom ticket sequence.
2. Focus on the most recent entries in `Bug Notes` first to catch the latest decisions/blockers.

### 2. Linked Request Review Rule
1. Open each linked bug/NFR in a new browser tab using its Mantis URL.
2. Review each linked ticket using the same TOP3 review rule used for the parent case.
3. Current UC-001 assumption: all requested linked bugs are initially in-scope.
4. Ask QA to reproduce each linked bug on the `Earliest Build Number` baseline.
5. Keep a bug in-scope when QA confirms it is reproducible.

### 3. PMDB Linking Rule (NFR case)
1. Check whether PMDB ID is already mentioned in the linked NFR ticket.
2. If present, copy/fill that ID into `PMDB ID` field in the TOP3 ticket.

### 4. Step 2 Output Artifact
1. Draft a mandatory `case profile` after reviewing TOP3 + linked Mantis + PMDB information.
2. Check must-have evidence existence: topology, configurations, debug logs.
3. Write a `Bug Note` summarizing what is available and explicitly request missing artifacts from supporters.

### 5. Exit Criteria
- No additional exit gate beyond completing the review and publishing the summary Bug Note.
