# Workflow Level 0 (AI-Only Dev + Ops Team)

## End-to-End Flow
1. Intake + Goal Framing (Product Owner Agent)
   - Inputs: user goal, constraints, budget, timeline
   - Outputs: problem statement, success metrics, scope, acceptance criteria

2. System Design (Solution Architect Agent)
   - Inputs: problem statement, constraints
   - Outputs: architecture diagram (text), component list, data flows, API contracts

3. Execution Plan (Tech Lead Agent)
   - Inputs: architecture, acceptance criteria
   - Outputs: task breakdown, milestones, coding standards, risk list

4. Build (Feature Engineer Agents)
   - Inputs: tasks, standards
   - Outputs: code, configs, prompts, tool wiring

5. Quality Gate (QA/Test Agent)
   - Inputs: code, acceptance criteria
   - Outputs: test plan, test cases, pass/fail report

6. Review Gate (Reviewer Agent)
   - Inputs: code, tests, architecture
   - Outputs: review findings, required fixes, approval

7. Deploy + Observe (DevOps/SRE + Data/Telemetry Agents)
   - Inputs: approved build
   - Outputs: deployment, monitoring, alerts, dashboards

8. Security Gate (Security/Compliance Agent)
   - Inputs: deployed system
   - Outputs: threat assessment, policy checks, audit notes

9. Documentation (Documentation Agent)
   - Inputs: final build
   - Outputs: runbooks, onboarding, usage guide

10. Meta-Loop: Agent Creation (Agent Designer + Evaluator)
    - Inputs: new agent requirements
    - Outputs: spec, prompt/tools, eval suite, benchmark report

## Default Hand-Off Rules
- Every stage outputs a short, structured artifact for the next stage.
- All gates (QA + Reviewer + Security) must pass before deployment.
- Failures return to the Tech Lead Agent for triage and re-assignment.
