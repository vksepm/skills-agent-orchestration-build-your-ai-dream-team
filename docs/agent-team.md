# Agent team

This document summarizes the custom agents I will use to build Mona's Project Pulse dashboard. I am using GitHub Copilot CLI in a Codespace to orchestrate the work.

## Team summary

| Agent | Model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 5.5 (copilot) | Breaks down the request, assigns file scopes, runs phases, and reports the final outcome. It coordinates the specialist agents but does not implement code. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 5.5 (copilot) | Researches the repository and produces implementation steps, file assignments, dependencies, edge cases, and validation expectations. It does not write code. | `.github/agents/planner.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements the assigned application logic, bug fixes, and code changes, keeping the work testable and aligned with repository patterns. | `.github/agents/coder.agent.md` |
| Designer | Gemini 3.7 Flash (copilot) | Shapes the dashboard experience with attention to usability, accessibility, information hierarchy, interaction flow, visual clarity, and responsive behavior. | `.github/agents/designer.agent.md` |

## How the team works together

1. The Orchestrator asks the Planner for a plan.
2. The Orchestrator splits the plan into phases based on file assignments and dependencies.
3. The Designer and Coder receive explicit file scopes. Work runs in parallel only when file scopes do not overlap and there are no data dependencies.
4. The Orchestrator verifies that the integrated result works together and reports the outcome.
5. I stage, commit, and push all changes myself through Copilot CLI prompts. No agent performs git operations.
