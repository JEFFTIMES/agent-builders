# Link Templates and Checks

Provide environment values through config instead of hardcoding hostnames.

## Config keys

- `MANTIS_BASE_URL`
- `JENKINS_BASE_URL`
- `INFOSITE_BASE_URL`
- `TOP3_TICKET_PATH_TEMPLATE` (default: `/bug_view_page.php?bug_id={caseId}`)
- `MANTIS_EDIT_PATH_TEMPLATE` (optional)

## Templates

- Top3 case page:
  - `{MANTIS_BASE_URL}{TOP3_TICKET_PATH_TEMPLATE}`
- Jenkins branch request/job:
  - `{JENKINS_BASE_URL}/job/{jobPath}`
- InfoSite branch/build page:
  - `{INFOSITE_BASE_URL}/?branch={branchName}` (example pattern)

## Verification checks

### Jenkins

- Branch creation request succeeded.
- `branchName` appears in job/build parameters or resulting branch metadata.

### Email

- Notification exists with matching `branchName` and expected case context.

### Mantis

- Top3 case field `Branch Merge List` contains `branchName`.

### InfoSite

- Branch page/index resolves and references `branchName`.

## Evidence fields to persist

- `objectRef`
- `fieldName`
- `accessLink`
- `rawValue`
- `capturedAt`
