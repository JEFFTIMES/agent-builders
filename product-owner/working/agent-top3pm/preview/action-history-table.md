| ID | Merged | Summary |
|---|---|---|
| Top3Build |  | 8928 |
| 1225001 | Mantis | (RADIUS enriched traffic log with 3GPP-specific attributes for enhanced RSSO solutions) |
| 1241381 | Mantis | (fg_7-4_logging_rsso_acc_msgs/build_tag_8923: ipv6 rsso traffic log did not include "rssoinfo" field) |
| 1241309 | Mantis | (fg_7-4_logging_rsso_acc_msgs/build_tag_8923: Two Class attribure, traffic log only show up one) |
| Top3Build |  | 8956 (for 3800G/3801G) |
| 1225001 | Mantis | (RADIUS enriched traffic log with 3GPP-specific attributes for enhanced RSSO solutions) |
| 1241381 | Mantis | (fg_7-4_logging_rsso_acc_msgs/build_tag_8923: ipv6 rsso traffic log did not include "rssoinfo" field) |
| 1241309 | Mantis | (fg_7-4_logging_rsso_acc_msgs/build_tag_8923: Two Class attribure, traffic log only show up one) |
| Top3Build |  | 8997 (for 3800G/3801G) |
| 1225001 | Mantis | (RADIUS enriched traffic log with 3GPP-specific attributes for enhanced RSSO solutions) |
|  |  |  |



| Row type | HTML signal | ID column | Merged column | Summary column | Mapping rule |
|---|---|---|---|---|---|
| Header row | tr.row-category | ID | Merged | Summary | Defines logical 3-column table header. |
| Build marker row | first td is Top3Build + second cell colspan="5" | Top3Build (label) | build value (e.g. 8928, 8956 (for 3800G/3801G)) | empty | Treat as a group marker row; build
number maps to Merged. |
| Linked issue row | first td contains <a ... bug_id=...> | bug id (e.g. 1225001) | source (e.g. Mantis) | issue summary text | Standard action entry row. |
| Spacer/empty row | first td empty and second cell &nbsp; | empty | empty | empty | Non-data row; ignore in normalized model. |