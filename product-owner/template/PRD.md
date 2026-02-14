# PRD.md

## Version

| Field         | Sample Details                 |
| ------------- | ------------------------------ |
| Product Name  | <To be defined>                |
| Version       | <1.0>                          |
| Date          | June 3, 2025                   |
| Product Owner | Agent Alex                     |
| Summary       | <What changes in this version> |

## Goals

*What measurable outcomes define success for the tool we are going to create, and how will we track progress toward them?*

- An AI Agent acts as an assistant, capturing scattered information, analysing the situation, conducting routine actions (schedule meeting, create branch, trigger image building),  writing progress notes and case summary, to help the PM.
- The AI Agent can accumulate domain knowledge while processing cases.
- The AI Agent can update the templates it used during the case, make it better for the next one.
- The AI Agent can report the issues and problems it is encountering while interacting with the existing applications and tools.
- The AI Agent can do self-assessment and report it.

## User 

*Who will use this agent, and what do they need from the tool?*

| Role        | Primary Needs                                                                             |
| ----------- | ----------------------------------------------------------------------------------------- |
| PMs         | Track case progress, conduct routine actions, write notes and summary                     |
| PM Director | Accumulate domain knowledge, update templates                                             |
| Developer   | Accumulate fix summary ( why the issue is triggered? How does the fix work?)              |
| QA          | Accumulate issue reproduction summary ( settings, traffic pattern, triggering conditions) |
| Agent Owner | Evaluate the performance of the agent, continuously improve the agent                     |

## Use cases

*What task the user's doing? What is the workflow? How does the user do the job across many existing applications?*

The user is a software development project manager working for a cybersecurity product company. They receive new feature requests or issue reports esclated by the customer-facing colleagues, call in develpers and QAs to form a temporary team and conduct a small software development project that implement a piece of code either enrich the features of an existing product, or fix a software bug, then build a image to deliver the solution. 

Typically, a project starts from initiating a temporary team. The Project Manager reads the ticket escalated by the product's end user, after getting a primary understand on the case, the PM creates a two-tier virtual groups to conduct a few turns of 'issue investigation - issue reproduction - software development - build test - image application' cycle to resolve the customer issue.

The core layer of the virtual team consists only software developers and QAs who are pertinent to the issue, the outter layer includes the customer-facing colleagues and even the customer. The PM instantiates two group chats, one only covers the core team members and the other covers all members, in MS Teams to facilitate the communication. 

Besides the MS Teams is used to conduct message exchange and voice meeting, other tools exist to facilitate the processing. 

- Mantis: an issue ticket system,  is used to document the procedure of the case and its subordinary bugs. 
- Gitlab: source code version control, is used to create feature/fix branches. Jenkins is used to build images. 
- InfoSite: an image repository, is used to save images. 
- Phab: a journal application, is used to document  QA history. 
- Outlook: email and calander.
- SharePoint: used to share media, such as video and large files. 

During the case processing, the PM reviews information that scattered among messages, emails theads, bug notes, QA journals to capture the status and interpret the current situation, and decide what action to do to push forward the processing. Then the PM takes actions, such as posting message, sending email, creating branch, triggering build, writing procedure note, to execute the decision or ask team members to execute the decision.  

During the processing, the PM needs to frequently document the progress and highlight the status in the Mantis ticket, which help the case stakeholders to monitor the case.

Once the case has been done, the PM writes a case summary document in Markdown format. The summary reflexes the procedure, highlights the turning points, such as issue reproduced moment, root cause found moment, and document what are the determinants promote the turning points.

## As-Is workflow

- The PM checks the tickets under TOP3 category in Mants, selects the new assigned ticket to enter the case.
- The PM reviews the ticket, establishing a two-tier virtual team, creating two MS Teams group chats to organize communication.
- The PM starts the "Investigation --- issue reprodution --- software development --- solution application" cycle. In a cycle, typically, the PM does such actions and write journals accordingly in the ticket:
    - Requests complement information, such as, the topology, configuration, traffic sniffs. from the customer-facing colleague.
    - Hosts meeting via MS Teams to discuss the situation with the core team; the outter team is involved into the discussion if needed. 
    - Makes a draft plan and schedule.
    - Requests the QA to set up the lab environment and reproduce the reported issues. If the issue is hard to replicate locally, requests remote sessions to invesitgate the issue on the customer's site.
    - Hosts meeting via MS Teams to review the issue reproduction/onsite investigation with the QA and the Dev, even the customer-facing guys if needed.
    - Iterates the investigation until the QA reproduces the issue locally.
    - Requests the developer to justify the root casue once the issue is reproduced. 
    - Once the root cause is identified by the devloper, draw a conclusion on the root cause and update the schedule.
    - Creates fix/feature branch from existing main branch for the developer.
    - Triggers image building to generate image once being informed by the developer.
    - Notifies the QA to validate the fix/feature build. If the build does not pass the validation, get it back to the developer. 
    - Release the build once the build passed all QA validation. 
- The PM writes a case summary and close the case.

## Problem

*What are the current pain points and inefficiencies that this tool needs to solve?*

**Painpoints:**

1. information scattered across many applications, hard to track.
2. Except the MS Teams, the other applications send no notification, the PM need to periodically visit the other applications to capture messages or status of undergoing jobs.
3. hard to write the case summary due to long duration and scattered information.
4. Case knowledge is not accumulated by roles.

## Existing Systems

*What are the existing systems, applications, tools that support the business currently?*

- Manits, a php-based web application that carries the ticket tracking.
- Jenkins, image building.
- Gitlab, version control.
- Infosite, a web application that hosts released images. 
- MS Teams, collaboration.
- Outlook, email.
- Phab, a web application that tracks QA tasks.

## Scope

*What features and boundaries will this tool include, and what will remain out of scope?*

**Need an AI agent to:**

- Monitor status, messages that scattered across silo applications, organize informaion to form a flowing whole picture, understand the current situation.
- Draft progress notes.
- Draft the case summary.
- Summarize meeting minutes.
- Remind next action to the PM.
- Schedule Teams meeting.
- Drafts notes, meeting minutes, and summary.

Non-goals: The agent doesn't take the other team member's task. The agent doesn't give instruction to other members .

## AI-Agent-in-the-loop workflow

*What is the expected workflow looks like when the AI agent participates in the work?*

1. Preview
    1. The Agent gets the information (URL, keywrods, etc.) about the new case from the PM.
    2. The Agent directs itself to the ticket, uses **TicketDict** to extract meta data from the top3 ticket and linked issue tickets. 
    3. Creates working folder **<TOP3 ID>-<Customer Name>**.
    4. Instantiates **Case Profile**.
    5. Drafts a **Case Preview** and sends via MS Teams or email it to the PM for a preview. Appends it to the list **Case Summaries** in the **Case Profile**.
2. Establish the virtual team
    1. Establishs a two-tier team, creating two MS Teams chat groups **<TOP3 ID>-Core** and **All:<TOP3 ID>-All** to organize communication.
    2. Pulls only the PM, developers and QA's to the Core group.
    3. Pulls all persons in the Core to the All, and adds the customer-facing colleagues in it as well.
    4. Discusses the candidates with the PM before pulling the persons to the groups. 
3. Project Plan
    1. Schedles a review meeting via MS Teams. Notifies the PM to discuss the situation and create project plan with the core team.  
    2. Participates in the meeting and collects information.
    3. Drafts a **Project Plan**, including actions and schedule, for the PM to confirm. Updates it to the top3 ticket as a new bug note and appends it to the list **Case Summaries** once get confirmed.
    4. During the review meeting, more persons, including the customer-facing colleagues, could be involved into the discussion if needed. Agent records the **attendences**. 
4. Issue Reproduction:  "Query information - Issue Reprodution" 
    1. If the QA and developer request for more background, writes an **Query**, posts it to the top3 ticket and to the All group, requesting complement information, such as, the **topology**, **firewall configuration**, **traffic sniffs**, from the customer-facing colleague. Checking the arrivals(Monitor the top3 mantis fields) of the files and notifying the QA and Dev to check them out.
    2. Sends an **Issue Reproduce Request message** via Core group, requesting the QA to set up the lab environment and reproduce the reported issues. 
    3. Monitors the Issue Reproduction, usually by querying the QA daily and checking the response message in the Core group. Writes **Investigation Summary**, appends it to the corresponding **Issues[id].investigation_logs[]** in **Case Profile** and updates it in the top3 ticket as a new bug note. 
    4. Gets back to ***4.A.*** if the QA/Developer needs any suppliment information from the customer-facing team.
    5. After running a few iterations, if the issue is hard to replicate locally, requests the customer-facing team to schedule a Remote Session, usually a collaborative meeting via MS Teams, to invesitgate the issue on the scene. 
    6. Paritcipates the remote session, collects information and writes a **Writes **Investigation Summary**, appends it to the corresponding **Issues[id].investigation_logs[]** in **Case Profile** and updates it in the top3 ticket as a new bug note. 
    7. Repeats the cycle of ***4.A. to 4.F.*** until the QA confirms that the issue has been reproduced.
    8. Schedules an **Issue Reproduction Review Meeting** and notifies the PM to review the issue reproduction/onsite investigation with the QA and the Dev, involves the customer-facing guys if needed. 
    9. Paticipates in the review meeting and collects information, writes **In-Progress Summary** and posts it as a bug note in the ticket, appends it to the list **Case Summaries** after review. 
5. Root Cause Justification: 
    1. Sends a** Root Cause Justification** request via Core group, requesting the developer to justify the root casue and triggering conditions. Monitors the reply from the developer. 
    2. Writes a **Wrp-up Summary** once the develpoer replies to the request,  posts the summary as a bug note to the top3 ticket and update the **Issues[id].wrap-up_summary** field, after the PM confirm it.
6. Implementation
    1. Proposes a **Branch Creation Metadata**, including branch name, product main branch,based branch point, etc., to the PM for a review.
    2. Creates the fix/feature branch in Jenkins, once confirmed.
    3. Checks creation confirmation in Outlook, notify the PM to handle the failure. Otherwise,sends the **Branch URL** to notify the developer in the Core group.
    4. Monitors the fix implementation. The Agent triggers image building to generate image once being informed by the developer.
    5. Notifies the QA to validate the fix/feature build. If the build does not pass the validation, get back to **6.D**. 
    6. Request a **Checklist** for the build, from the QA, once the build passes all QA validation.
7. Release
    1. Records the download URL of the images from infosite.com.
    2. Attaches the Checklist to the top3. 
    3. Writes **Release Note** and post it to the top3. 
    4. Writes a **Final summary** and reviews it with the PM. updates it to the top3 ticket and appends it to the list **Case Summaries**.
    5. Updates the status to solution sent in the top3 and closes the case.

## Requirements 

*What are the functional and technical requirements needed to deliver the core flows?*

- Creating skills, including prompt, tool scripts, etc., to help the agent access the existing applications.
- It is viable to add skills to the agent.
- The agent holds a dedicated Knowledge Base that accumulates domain knowledges whlie processing cases.
- The agent pickups domain knowledges when executing next case.
- The agent starts its work from revises the templates nvoice draft generation with export and audit trail.

## Metrics

*What key performance indicators will help us measure whether this tool is delivering value and meeting its goals?*

- Time compliance, utilization (billable vs. non-billable), forecast vs. actuals, invoicing cycle time and write-offs.