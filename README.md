# agent_skills

Wade's setup for one multipurpose Claude agent. You talk to Claude normally.
It works out whether you want an answer or a build, and runs the right flow
without being told which skill to use.

- `CLAUDE.md`: the agent's instructions. Short and direct, written for the model.
- `README.md`: this file. The same rules explained for people.

## 🔌 How it loads

`~/.claude/CLAUDE.md` imports this repo's `CLAUDE.md`:

```
@/home/wade/Documents/agent_skills/CLAUDE.md
```

So the rules apply in every project. Edit `CLAUDE.md` here, not the global file.
The global file still holds the shared rules: PM role, GitHub issue process,
project board, subagent model routing, and reply style.

## 🧰 What is installed

| Tool | Role |
|---|---|
| Superpowers plugin (`superpowers@claude-plugins-official`) | Build flow skills: brainstorm, plan, subagents, TDD, review |
| Context7 MCP (user scope) | Current library and framework docs |
| ISO 24495 plugin + output style | Plain-language replies |

GSD was removed on 2026-10-25 so it does not compete with Superpowers.
Backup: `~/.claude/backups/gsd-2026-10-25/`.

## 🧭 Modes

Claude sorts every message into one mode first.

| You say | Mode | What happens |
|---|---|---|
| "What is…", "How does…", "Compare…" | 🔎 Research | Looks it up, cites sources |
| "Let's build / add / fix / change…" | 🛠️ Build | Asks questions → spec → issue → plan → subagents |
| An error, a failing test, "why is X broken?" | 🐞 Debug | Finds the root cause, then Build |
| A one-line change in a known file | ⚡ Quick | Does it directly |
| Chat or a question it can answer from context | 💬 Answer | Replies directly |

## 🔎 Research mode

- Looks up facts instead of answering from training memory, because docs change.
- Context7 for library docs. Web search and fetch for everything else.
- Prefers official sources over blog posts.
- Labels answers that come from memory.
- Ends with a list of sources.

## 🛠️ Build mode

1. Reads the code, docs, and issues first.
2. Asks you questions until nothing is assumed (`superpowers:brainstorming`).
3. Plays the design back and waits for your OK.
4. Saves the spec and posts it as a GitHub issue.
5. Works in its own worktree on branch `claude/<slug>-<N>`.
6. Writes a step-by-step plan (`superpowers:writing-plans`).
7. Sends one subagent per task, with a review between tasks
   (`superpowers:subagent-driven-development`).
8. Uses tests first, then proves the work passes before saying done.
9. Code review → PR → CI green → merge.

## ✏️ Changing the rules

1. Edit `CLAUDE.md` in this repo. Keep it short and direct. No emoji.
2. Update this README to match.
3. Start a new Claude session to load the change.
