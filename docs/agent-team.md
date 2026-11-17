# Agent team

For Mona's Project Pulse dashboard, I am using a small custom agent team defined under `.github/agents/` and orchestrated with GitHub Copilot CLI in a Codespace.

- Planner — `Claude Opus 5.5 (copilot)`
  - Responsibility: research the repo, identify dependencies and edge cases, and produce an implementation plan that the orchestrator can break into phases.
  - Definition: `.github/agents/planner.agent.md`

- Orchestrator — `Claude Opus 5.5 (copilot)`
  - Responsibility: manage the workflow, delegate work to specialist agents, keep phases non-overlapping, and verify that the final result fits together.
  - Definition: `.github/agents/orchestrator.agent.md`

- Designer — `Gemini 3.7 Flash (copilot)`
  - Responsibility: focus on UI/UX, accessibility, information hierarchy, interaction flow, and polished dashboard styling for Project Pulse.
  - Definition: `.github/agents/designer.agent.md`

- Coder — `GPT-5.5 (copilot)`
  - Responsibility: implement the code changes, fix logic bugs, and build the app behavior within the scope assigned by the orchestrator.
  - Definition: `.github/agents/coder.agent.md`

This team gives me a clear split between planning, orchestration, design, and implementation while keeping the work coordinated through GitHub Copilot CLI in a Codespace.
