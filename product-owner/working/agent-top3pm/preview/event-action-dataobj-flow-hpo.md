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