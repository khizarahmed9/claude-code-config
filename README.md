# claude-code-config

My global [Claude Code](https://claude.com/claude-code) setup. Claude plans work as GitHub issues, builds them in parallel with subagents, reviews every PR, and writes the smallest code that works.

> **I run Claude Code in an isolated Ubuntu VM**, not on my main machine. I don't trust an agent with my real files, so the VM is the safety net: if it breaks something, I reset the VM. That lets me give Claude a lot of freedom (auto mode, standing permission to spawn subagents, push to GitHub) without worrying about my main machine.

## What's in here

| File | Goes to | What it does |
|---|---|---|
| `CLAUDE.md` | `~/.claude/CLAUDE.md` | Global rules loaded in every session |
| `agents/issue-worker.md` | `~/.claude/agents/` | Subagent: builds one issue on its own branch, opens a PR |
| `agents/pr-reviewer.md` | `~/.claude/agents/` | Subagent: reviews one PR, read-only |
| `settings.json` | `~/.claude/settings.json` | Plugins, theme, and no "Co-Authored-By: Claude" lines |

## How it works

1. **Every session** turns on ADHD mode: next action first, numbered steps, no fluff.
2. **New project:** Claude asks which GitHub repo to use, then splits the work into small issues. Each issue can be demoed on its own and lists its blockers as real GitHub "blocked by" links. You approve the list once.
3. **Build:** one `issue-worker` per unblocked issue, in parallel, each in its own git worktree. One issue = one branch = one PR that closes the issue.
4. **Review:** `pr-reviewer` checks each PR for bugs, missing tests, security problems, leaked personal info and attribution lines. Then it gets squash-merged.
5. **Wrap-up:** Claude lists the shortcuts it took and asks if you want issues for them.

Code style comes from **ponytail**: the smallest change that fully solves the task.

Privacy rules: commits use one name plus a GitHub noreply email. Claude never puts your real email, paths, tokens or secrets in commits, issues, PRs or outside requests.

## Install

Needs: Claude Code, git, and [`gh`](https://cli.github.com) **2.94 or newer** (for `--blocked-by` and `--parent`). Ubuntu's own `gh` package is too old, so use GitHub's apt repo.

1. Clone and back up your current config:
   ```sh
   git clone https://github.com/khizarahmed9/claude-code-config.git
   cd claude-code-config
   mkdir -p ~/claude-backup
   cp -r ~/.claude/CLAUDE.md ~/.claude/agents ~/.claude/settings.json ~/claude-backup/ 2>/dev/null
   ```
2. Copy the files in:
   ```sh
   mkdir -p ~/.claude/agents
   cp CLAUDE.md ~/.claude/
   cp agents/*.md ~/.claude/agents/
   cp settings.json ~/.claude/   # overwrites yours: merge by hand if you have your own settings
   ```
3. **Put in your own identity.** Replace the name and noreply email in `~/.claude/CLAUDE.md` and both agent files. Find your noreply email at GitHub → Settings → Emails (`<id>+<username>@users.noreply.github.com`). Then:
   ```sh
   git config --global user.name "Your Name"
   git config --global user.email "<id>+<username>@users.noreply.github.com"
   ```
4. Install the plugins:
   ```sh
   claude plugin marketplace add DietrichGebert/ponytail
   claude plugin install ponytail@ponytail
   claude plugin marketplace add pbakaus/impeccable
   claude plugin install impeccable@impeccable
   ```
5. Log in to GitHub: `gh auth login`.
6. Start a new `claude` session. It should open in ADHD mode. Say "new project" and it should ask for a repo.

## Extras I use (optional)

- **`i-have-adhd` skill:** a third-party skill (not from Anthropic) synced to my claude.ai account. It's not included here. Install it yourself, or remove the "Session start" rule from `CLAUDE.md`.
- **impeccable:** a frontend design plugin. Not needed for the workflow.

## Credits

- [ponytail](https://github.com/DietrichGebert/ponytail): minimal-code rules, also added to subagents.
- [mattpocock/skills](https://github.com/mattpocock/skills): issue-splitting ideas (small demoable issues, blocker links). Copied into `CLAUDE.md` so Claude runs them itself.
