› create a new md file, event-action-dataobj-flow.md ,to log what we will discuss
  let me describe what the flow looks like.


  first, let me define data objects
  - Jenkins.BranchRequestPage
  - Mantis.Top3CasePage
  - Outlook.EmailNotify
  - user.pm
  then, define the actions
  - updatesTop3BranchName
  - clickSumbitButton
  - fillForm
  - checkEmail
  - checkBranchName
  then events
  - branchApproved
  - branchNotExist
  - caseStart
  - regularlyCheckEmail
  - branchNameReady
  - branchNameUpdated

  let me describing the flow

  (caseStart) [triggers] <user.pm> [checkBranchName] on <Mantis.Top3CasePage> [emits] (branchNotExist)
  (branchNotExist) [triggers] <user.pm> [fillForm] on <Jenkins.BranchRequestPage> AND [clickSubmitButton] on <Jenkins.BranchRequestPage> [emits] (branchApproved)
  (regularlyCheckEmail) [triggers] <user.pm> [checkEmail] <Outlook.EmailNotify> which [carries] (branchApproved) [emits] (branchNameReady)
  (branchNameReady) [triggers] <user.pm> [updateTop3BranchName] in <Mantis.Top3CasePage> [emits] (branchNameUpdated)


› regarding domain data model, I want to split them into two layers(using layers might not precisely), the core layer abstract the stable entities as you mentioned, add a presentation
  layer. an entity in this layer (1) maps the corresponding business entity's properitis to the fields of the current human-application-interface,such as Manits.Top3CasePage and
  Jenkins.BranchRequestPage, (2) guides the upcoming agent interacting with the applications in a humam-mimic way.
  Is this consideration towards a correct direction and practical?
  any other guys doing so?
  