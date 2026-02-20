# Data Model - Top3 Case v0.1 - agent-top3pm

## Entity: Top3Case
- `case_id` (string)
- `title` (string)
- `status` (enum)
- `severity` (enum)
- `reporter` (string)
- `assignee_group` (string)
- `assignee` (string, optional)
- `created_at` (datetime)
- `updated_at` (datetime)
- `business_objective` (text)
- `customer_context` (text)

## Entity: TechnicalExecution
- `branch_name` (string)
- `base_version` (string)
- `build_job` (string)
- `build_number` (string)
- `build_result` (enum)
- `artifact_url` (string)

## Entity: ValidationRecord
- `test_context` (text)
- `repro_steps` (text)
- `observed_result` (text)
- `expected_result` (text)
- `evidence_link` (string)

## Entity: CrossRef
- `mantis_ids` (array)
- `forticare_id` (string, optional)
- `phabricator_task_id` (string, optional)
- `teams_thread_ref` (string, optional)

## AI Agent Fields
- `risk_score` (0-100)
- `blocker_flags` (array)
- `next_best_actions` (array)
