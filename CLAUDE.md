# Global rules (apply to every session and project)

## Session start
- At the start of every session, invoke the `i-have-adhd` skill (Skill tool, `skill: "i-have-adhd"`) before doing anything else. Keep it on unless I say "stop adhd mode".

## Ponytail + ADHD together
- Ponytail decides *what code to write* (smallest complete change). ADHD mode decides *how replies look* (next action first, numbered steps).
- Keep ponytail's closing line (what was skipped / risks) at the end of every reply.

## Project kickoff (new project or folder with no git remote)
1. Ask me for the GitHub repo to contribute to. If I don't have one, offer `gh repo create` (private by default).
2. Break the work into the smallest set of features that does the core job (ponytail rule 1). List extras you're skipping in one line so I can add them back.
3. Slice features vertically: each issue is a thin path through every layer (data, logic, UI, tests) that I can demo on its own. No "all the schema" / "all the API" issues. Do setup/refactor work first ("make the change easy, then make the easy change"). Size each issue to fit one fresh subagent context; merge tiny ones.
4. Create a parent issue for the project/milestone, then the feature issues blockers-first as its sub-issues: `gh issue create --parent <parent#> --blocked-by <a,b> --label enhancement` (or `bug`). Body: what it does, a "Demo:" line (what I can see working when done), and a "done when" checklist. Use native `--blocked-by` links, not "Blocked by" text.
5. Show me the numbered issue list with its blockers before writing code. Ask once: right size, right blockers, anything to merge or split? Then go.

## Working issues (use subagents)
- You have my standing permission to spawn subagents for this workflow.
- One issue = one branch (`<issue#>-short-name`) = one PR whose body says `Closes #<n>`.
- The frontier = open sub-issues whose blockers are all closed (check with `gh issue view <n> --json blockedBy`). Spawn one `issue-worker` (with `isolation: "worktree"`) per frontier issue, in parallel. When merges close issues, recompute the frontier and spawn the next batch.
- If an issue turns out too big or the plan was wrong, stop and update the issues with me rather than letting a worker redesign it.
- After a worker opens its PR, spawn the `pr-reviewer` agent on it. Fix what it flags, then squash-merge (`gh pr merge --squash --delete-branch`).
- Pull main after each merge so the next worker starts from current code.
- Tiny changes (typo, one-liner) can skip subagents but still get an issue and a `Closes #<n>` commit.
- After all planned issues are merged, run `/ponytail-debt` and ask me whether to turn each `shortcut:` comment into a `tech-debt` issue.

## Commit identity and attribution
- Author every commit as `Khizar Ahmed <179360065+khizarahmed9@users.noreply.github.com>`.
- Never add `Co-Authored-By: Claude`, "Generated with Claude Code", or any Claude/Anthropic attribution to commits, PRs, or issues.

## Privacy
- Never put my personal info (real email, account emails, home dir paths, machine details, tokens, other projects) into commits, issues, PRs, code, docs, or requests to outside services unless I explicitly say so.
- The only identity allowed in git/GitHub is the name and noreply email above.
- Never commit secrets: check for `.env`, keys, and credentials before staging; add them to `.gitignore`.

## Time Estimates
- We work at least 10x faster than a conventional "engineering day" estimate assumes. This has been measured over hundreds of sessions.
- Estimate in session hours at that pace, not in engineer days or weeks. If you start from a conventional figure, divide it by 10 and say so ("about 20 days conventionally, about 2 days of sessions for us").
- The pace is lower for work bound by things that don't speed up: My decisions, peer reviews, rehearsals on real data, long test runs, browser passes, external systems. Name those separately rather than inflating the whole estimate.
- Agent peers and sub-agents estimate conventionally: convert their figures before passing them on, and say you did.
