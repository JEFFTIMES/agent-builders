# NFR Field Candidates - Mantis 1225001 (v0.1)

Source ticket: `working/agent-top3pm/assets/top3apps/1225001 -- Mantis.html`

## 1. Core ticket fields (high confidence)
- `nfrId`: `1225001`
- `product`: `[NFR] FortiOS`
- `severity`: `S3-Important`
- `dateSubmitted`: `2025-11-10 09:26:23`
- `lastUpdate`: `2026-02-09 22:52:03`
- `solution`: `MSSP,NGFW,Telco` (Mantis field label: `Solution`)
- `reporterDisplay`: `Tom Walker (twalker)`
- `viewStatus`: `public`
- `eta`: `2026-02-28`
- `priority`: `high`
- `resolution`: `open`
- `status`: `Concept Commit`
- `duplicateIssueId`: empty
- `summary`: `RADIUS enriched traffic log with 3GPP-specific attributes for enhanced RSSO solutions`
- `description`: long text block present (customer problem, requirements, implementation suggestions, topology/competitor notes)
- `additionalInformation`: `Branch: br_8-0_rsso_3gpp_logging`

## 2. Business/customer linkage fields (candidate)
- `salesforceOppIds`: `006Hr00001Vvh8fIAB`
- `nfrRef`: empty (label `NFR`)
- `affectedCustomer`: `Bharti Airtel`
- `salesforceAccountIds`: `0018000000NS5DEAA1`

## 3. Delivery/support custom fields shown on page
- `cssTicketNumbersWithSWVersion`: empty
- `cssRequest`: empty
- `cssNotes`: empty
- `forumId`: empty
- `rootCauseEcoMantis`: empty
- `devQaStatus`: empty
- `devStatus`: empty
- `patchCompatibility`: empty
- `pmdbId`: empty
- `changeOrderLink`: empty
- `relatedProjectIds`: empty
- `qaId`: empty
- `askbotLink`: empty
- `addToKnownIssueInRN`: empty
- `rnCategoryDescription`: empty
- `rnSectionDescription`: empty
- `publishStatus`: empty
- `publishTitle`: empty
- `customerFacingDescription`: empty
- `workaround`: empty
- `triggerCondition`: empty

## 4. Attachment metadata (candidate)
- `attachedFiles`:
  - `29061-h60.docx` (1,428,778 bytes) @ `2025-11-10 09:56`
  - `RADIUS-3gpp-logging.png` (137,282 bytes) @ `2025-11-12 01:46`
  - `packet-capture.pcap` (13,724 bytes) @ `2025-11-25 03:43`

## 5. Immediate modeling suggestion for NFR entity
- Keep (baseline): `nfrId`, `product`, `solution`, `summary`, `description`, `status`, `severity`, `reporterId`, `dateSubmitted`, `lastUpdate`, `eta`, `affectedCustomer`, `salesforceOppIds`, `salesforceAccountIds`, `additionalInformation`.
- Optional: attachment list, custom delivery fields.
- Skip for now: mostly-empty custom fields unless needed for downstream workflow automation.
