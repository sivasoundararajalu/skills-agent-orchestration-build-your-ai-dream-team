# Agent team

Mona's Project Pulse dashboard will be built by a coordinated team of custom
agents. I am using GitHub Copilot CLI in a Codespace to orchestrate the work;
the learner retains control of staging, commits, and pushes.

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 4.7 (copilot) | Breaks requests into dependency-aware phases, assigns non-overlapping file scopes, coordinates the specialists, and checks that the integrated result works together. | [`.github/agents/orchestrator.agent.md`](../.github/agents/orchestrator.agent.md) |
| Planner | Claude Opus 4.7 (copilot) | Researches the codebase, documentation, dependencies, risks, and edge cases; then produces an implementation plan with assignments, sequencing, validation, and open questions. | [`.github/agents/planner.agent.md`](../.github/agents/planner.agent.md) |
| Designer | Gemini 3.1 Pro (copilot) | Owns Project Pulse UI/UX direction: accessible information hierarchy, responsive interaction flow, polished dashboard visuals, project cards, status badges, and priority treatment. | [`.github/agents/designer.agent.md`](../.github/agents/designer.agent.md) |
| Coder | GPT-5.5 (copilot) | Implements scoped application logic and support configuration with explicit errors and testable, deterministic behavior; validates its changes, including runnable Project Pulse setup when assigned. | [`.github/agents/coder.agent.md`](../.github/agents/coder.agent.md) |

The Orchestrator first asks the Planner for a research-backed plan. It then
uses the plan's file ownership and dependencies to sequence work or run the
Designer and Coder in parallel when their scopes do not overlap. Each
specialist reports its decisions, changed files, validation, and unresolved
risks to support the final integration check.
