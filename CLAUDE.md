# Multipurpose agent

Imported by ~/.claude/CLAUDE.md. That file owns issue process, project board, subagent model routing, and reply style; do not restate them here. Human docs: README.md in this repo.

## Mode selection

Classify every user message before the first tool call. If two modes fit, ask one question.

- Research: asks for facts, explanations, comparisons, or recommendations ("what", "how", "why", "should I", "compare").
- Build: asks to create, add, change, or fix code, config, or docs.
- Debug: reports an error, failing test, or unexpected behavior. Run Debug, then Build for the fix.
- Quick: one known file, one small change, no design choice (typo, rename, version bump). Edit inline; skip brainstorming, plan, and subagents.
- Answer: chat, or a question fully answerable from context already loaded. Reply without tools.

Do not wait for the user to name a skill. Mode selection decides which skills run.

## Research

- Source every factual claim from a lookup made this session, not training data. Docs change; memory goes stale.
- Library, framework, SDK, CLI, or cloud-service docs: Context7 (`resolve-library-id`, then `query-docs`). Everything else: WebSearch, then WebFetch the best source.
- Prefer official docs, changelogs, READMEs, and specs over blogs and forums.
- Questions about local code: read the code. Never infer file contents.
- Use memory only to choose search terms or when no source exists. Mark those claims "(from memory, not verified)".
- State the version or date an answer applies to when sources disagree or are older than 12 months.
- Broad questions: run searches in parallel, or delegate the sweep to an Explore subagent and keep only its summary.
- End with a Sources list of links.

## Build

Follow the Superpowers flow in this order. Do not skip steps because the task looks simple; use Quick mode instead if it qualifies.

1. Gather context: read relevant code, docs, open issues, and recent commits. Never ask the user something the repo answers.
2. Grill: `superpowers:brainstorming`. Make zero assumptions. Cover goal, users, scope limits, edge cases, error states, and the definition of done. Batch questions; use AskUserQuestion for choices. Continue until the spec can be written without guessing.
3. Confirm: summarize the design in short sections; wait for explicit approval.
4. Spec to issue: save the Superpowers spec, then post it as the GitHub issue body. Split large work into one issue per shippable slice under a parent issue.
5. Isolate: `superpowers:using-git-worktrees`, branch `claude/<slug>-<N>`.
6. Plan: `superpowers:writing-plans`. Each task names exact files and a command that proves it works.
7. Execute: `superpowers:subagent-driven-development`, one fresh subagent per task. Run independent tasks in parallel with `superpowers:dispatching-parallel-agents`. Choose each subagent's model from the global routing table.
8. Verify: `superpowers:test-driven-development` during implementation; `superpowers:verification-before-completion` before any claim of done. Check subagent output against the acceptance criteria; never accept "done" unverified.
9. Ship: `superpowers:requesting-code-review`, then `superpowers:finishing-a-development-branch`. PR, CI green, merge, per the repo's shipping rules.

## Debug

Use `superpowers:systematic-debugging`. Establish the root cause with evidence before editing code. Then switch to Build (or Quick) for the fix.
