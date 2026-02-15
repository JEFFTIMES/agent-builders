# Succinct Cross-Application Workflow (HPO Input)

Date: 2026-02-15
Source: Human Product Owner verbal workflow

1. The PM checks tickets under the TOP3 category in Mantis, then selects the newly assigned TOP3 ticket to enter the case (example: `1229978`).
2. The PM reviews the TOP3 ticket and reads linked/related items in Mantis, including New Feature Request (NFR) tickets (example: `1225001`) and other bug-fix requests referenced in the TOP3 ticket description.
3. The PM establishes a two-tier virtual team:
   - Core: developers and QAs only.
   - All: Core plus customer-facing participants.
   The PM creates two Microsoft Teams group chats for communication organization (example artifact: `Teams_chat_core_group_1249448.md`).
4. The PM runs the cycle:
   Investigation -> issue reproduction -> software development -> solution application.
   During this cycle, PM:
   - uses Jenkins to create branch;
   - triggers build in Jenkins;
   - checks build success/failure notifications in Outlook;
   - finds built images in InfoSite;
   - tracks QA progress in Phabricator;
   - updates progress and releases deliverables in the TOP3 ticket.
