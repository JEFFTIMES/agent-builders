# 1. Preview

## Inputs

- The documents about the business, system and pains.
- The dialogue with the Human Product Owner.

## What to do

- Find documents under ***assets/***, ask the HPO to provide if not.
- Read the document to get an overview of the business and systems.
- Interpret a succinct workflow from the document, and enumerate the applications are involved in the workflow.
- Present the workflow and a application list to the HPO.
- Query the HPO for:
    1. an end-to-end workflow that skewers across the applications, so application handoffs and dependencies are explicit. **Note:** Don't interrogate details of the workflow for now, do it in workflow step 2 and 5.
- Review documents and dialogue to capture:
    1. Business Objective
    2. As-Is workflow
    3. Roles participates in the workflow
    4. Business Rules
    5. Managed Objects and their properties
       - Use ontology-style extraction: declare entities (e.g., Top3Case, NFR, Issue, Branch, Build), list their properties, and define typed links between entities (including link keys such as ticket IDs, branch names, build tags).
       - Specify relation cardinality where possible (e.g., one Top3Case to many Issues/Builds).
       - For every property, capture and record the exact source (system/page/field/section), and ask the HPO to confirm any source that is ambiguous.
       - Before finalizing managed objects, double-check property-to-source mapping with the HPO.
    6. Systems and tools are used
    7. Cross-application Managed Object Data handoff
    8. As-Is Problems (Constraints)
- Let the HPO review your capture, you update them accordingly.
- Jot conversation logs.
- Draft business objective, constraints, hypothesis under AI agent being involved scenario.


## Outputs

Save work products in ***working/<product>/preview/***

- Workflow under system context --> ***workflow-<version>.md***  
- Business Rules --> ***biz-rules-<version>.md***
- As-Is systems and tools --> ***apps-<version>.md***
- User-Application interaction --> ***user-interact-<sys/app>-<versino>.md***
- cross-application MO data handoff --> ***x-app-mo-data-handoff-<version>.md***
- Managed Objects data model  --> ***data-model-<scope>-<version>.md***
- Identified problems --> ***problems-<version>.md***
- Logs --> ***preview_log.md***
- Business Objective, Constraints and Hypothesis --> ***biz-obj-cons-hypo-<version>.md***
