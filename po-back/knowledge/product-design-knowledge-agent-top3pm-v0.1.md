# Product Design Knowledge - agent-top3pm (v0.1)

Date: 2026-02-18
Source Stage: Current project execution (Step 0-2)

## 1. Interview Method Improvements
- Effective opening protocol:
  1. confirm interviewee identity/title,
  2. replay succinct baseline workflow,
  3. ask interviewee to enrich/correct before deep questions.
- This reduces ambiguity and anchors conversation on shared context.

## 2. Documentation Pattern That Works
- Split outputs by artifact type early:
  - workflow (`exist_workflow.md`),
  - evidence Q/A (`QA_log.md`),
  - use-case tracker (`usecases.md`),
  - field dictionary (`top3-mantis-field-dict.md`),
  - non-current-step ideas (`sparks.md`).
- Keep requirement-like sparks out of interview guide to avoid scope pollution.

## 3. Structuring Multi-Variant Work
- Explicitly separate UC-001 (NFR) and UC-002 (Bug Fixing).
- Avoid merging variant-specific rules too early.
- Use reminders in `sparks.md` for deferred deep dives.

## 4. Cross-System Requirement Capture Technique
- Convert each critical field into:
  - semantics,
  - implicit linked systems,
  - usage in each linked system,
  - interpretation rule.
- This creates a reusable bridge from interview evidence to PRD functional requirements.

## 5. Process Refinements Already Applied
- Added Step 1 instruction in `AGENTS.md` to request a succinct cross-application workflow from HPO.
- Added Step 2 protocol in `AGENTS.md` to standardize interview opening sequence.
- Added reusable template `template/usecases.md` for structured use-case capture.

## 6. Remaining Process Gaps
- Need a dedicated template for case profile artifact.
- Need explicit checklist for evidence sufficiency (topology/config/debug logs) per case type.
- Need consistent criteria for `Conditional` vs `Optional` field behavior across variants.
