# 2. User Interview

**Goal:** collect high-signal qualitative evidence on user goals, pain points, and context.

You will interview the end user of both the current applications and the targeting product.

## Inputs

- the work products produced by step 1, saved in ***preview/**.
- Dialogue with the interviewee and the HPO.
- Supplement documents provided by the interviewee.

## What to do

### Prepare playbook

- Review the step 1 work products, draft ***interview-playbook.md*** that consists of: 
    a. part 1: a brief on your understanding of the situation:
        1. Business Context
        2. System Context
        3. As-Is Workflow
        4. Selected 3 Problems
        5. Preset Goals for the target product
        6. Success Metrics
    b. part 2: a thought represents how could you collect information to help you detail the current workflow, extract business rules, extract and design ai-agent-in-the-loop workflow, such as 
        1. roles responsibilities and skills
        2. business rules
        3. user-application interacting details
        3. cross-application data handoff details
        3. managed object properties
        4. UIs or APIs that you can use to design the automation

- Confirm the playbook with the HPO.

### Conduct interview

- Brief the part 1 to the interviewee, setting up backdrop.
- Follow your though, lead the conversation, interrogate the interviewee to collect information.  
- Summarize and log conversation by steps of the As-Is workflow.

### Wrap-up interview

- Use the information you collected from the interview to update the following work products that were initiated in step 1.
    1. ***workflow-<version>.md***  
    2. ***biz-rules-<version>.md***
    3. ***apps-<version>.md***
    4. ***user-interact-<app>-<versino>.md***
    5. ***x-app-mo-data-handoff-<version>.md***
    6. ***data-model-<mo>-<version>.md***
    7. ***problems-<version>.md***

- Review the updates with the HPO.
- Write use cases to glue the collected fragment information, representing them in human-friendly stores.


## Outputs

Save work products in ***working/<product>/interview/***

- ***interview-log-<interviewee>.md***
- ***workflow-<interviewee>-<version>.md***  
- ***biz-rules-<interviewee>-<version>.md***
- ***apps-<interviewee>-<version>.md***
- ***user-interact-<app>-<versino>.md***
- ***x-app-mo-data-handoff-<interviewee>-<version>.md***
- ***data-model-<mo>-<interviewee>-<version>.md***
- ***problems-<interviewee>-<version>.md***
- ***use-case-<interviewee>-<version>.md***
