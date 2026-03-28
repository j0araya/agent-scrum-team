# Agent Team

A multi-agent software development team powered by OpenCode. Each agent has a distinct role and expertise.

## Agents

| Agent | Command | Role |
|-------|---------|------|
| CEO | `project:ceo` | Strategic planning, requirements, task delegation |
| UI Designer | `project:ui-designer` | Interface design, wireframes, design system |
| Scrum Master | `project:scrum-master` | Clarifying questions, sub-task breakdown, Jira subtasks |
| Developer | `project:developer` | Architecture, implementation, code |
| QA | `project:qa` | Testing, bug reports, sign-off |
| Code Reviewer | `project:code-reviewer` | Best practices gate — approves merge or sends back to dev board |
| Jira Board | `project:jira-board` | Direct Jira board interactions |
| Team Sprint | `project:team-sprint` | Run all agents in sequence for a full sprint |

## Usage

### Run from this directory

```bash
cd ~/agent-team
opencode
```

### Use individual agents

Press `Ctrl+K` to open the command dialog, then select:

- `project:ceo` — Start with the CEO to plan your project
- `project:ui-designer` — Get UI/UX design specs
- `project:scrum-master` — Break DEV task into subtasks, ask clarifying questions
- `project:developer` — Get code implementation
- `project:qa` — Get test plan and QA report
- `project:code-reviewer` — Run the merge gate (approve or reject to dev board)
- `project:jira-board` — Interact directly with the qwerteam Jira board
- `project:team-sprint` — Run the full team in one session

### Run via CLI (non-interactive)

```bash
opencode -p "$(cat .opencode/commands/team-sprint.md)" 
```

Or with a specific agent:

```bash
cd ~/agent-team
opencode run "project:ceo" --arg PROJECT_GOAL="Build a todo app with user auth"
```

## Workflow

```
User Input
    |
    v
[CEO Agent] ──── Project Brief + Jira Issues created
    |                    |
    v                    v
[UI Designer]    [Scrum Master]
Design Specs      Clarifying Questions
                  Sub-task Breakdown
                  Jira Subtasks created
                       |
                       v
                 [Developer]
                 Code + Arch
                       |
                       v
                  [QA Agent]
              Test Plan + Bug tickets
                       |
                       v
             [Code Reviewer Agent]
              Best Practices Gate
               /              \
       APPROVED             REJECTED
          |                    |
      Merge PR         Back to Dev Board
                       Jira task → To Do
                       (fix & re-submit)
```

## GitHub Actions: Automated Merge Gate

The `.github/workflows/code-review-gate.yml` workflow runs automatically on every pull request to `main`, `master`, or `develop`.

**What it does:**
1. Extracts the PR diff
2. Runs the Code Reviewer Agent via OpenCode
3. Posts a detailed review comment on the PR
4. **If APPROVED** — approves the PR and allows merge
5. **If REJECTED** — requests changes, blocks the merge, and posts a dev board task list

**Required GitHub secrets:**
| Secret | Purpose |
|--------|---------|
| `ANTHROPIC_API_KEY` | Powers the Code Reviewer Agent |
| `GITHUB_TOKEN` | Auto-provided by GitHub Actions |

## Project Structure

```
agent-team/
├── .opencode.json              # OpenCode config
├── .opencode/
│   └── commands/
│       ├── ceo.md              # CEO agent
│       ├── ui-designer.md      # UI Designer agent
│       ├── scrum-master.md     # Scrum Master agent (subtasks + clarifying questions)
│       ├── developer.md        # Developer agent
│       ├── qa.md               # QA agent
│       ├── code-reviewer.md    # Code Reviewer agent (merge gate)
│       ├── jira-board.md       # Jira board manager
│       └── team-sprint.md      # Full team orchestrator
├── .github/
│   └── workflows/
│       └── code-review-gate.yml  # GitHub Actions merge gate
└── README.md
```

## Customizing Agents

Edit any `.md` file in `.opencode/commands/` to adjust an agent's behavior, persona, or output format.

Each agent supports named arguments using `$ARGUMENT_NAME` placeholders. When you run a command, OpenCode will prompt you for each argument value.
