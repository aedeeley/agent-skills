---
name: github-workflow
description: Use when committing changes, opening or reviewing pull requests, creating branches, writing commit messages, or resolving merge conflicts in a Git/GitHub-based project. Trigger on "commit this", "open a PR", "write a commit message", branch naming questions, or any git/gh CLI operation.
---

## Instructions

### Branch naming
Use `type/short-description`, lowercase, hyphenated:
- `feat/dispatch-driver-view`
- `fix/qsv-transcode-crash`
- `chore/update-deps`
- `docs/readme-setup`

Match `type` to the same set used in commit messages (see below) so branch and commit history stay legible together.

### Commit messages — Conventional Commits
Format: `type(scope): short summary` (imperative mood, no trailing period, ~50 char summary line).

Types: `feat`, `fix`, `refactor`, `chore`, `docs`, `style`, `test`, `perf`, `build`, `ci`.

```
feat(dispatch): add real-time driver location markers
fix(fileflows): prevent duplicate pgsrip runs on re-queued jobs
chore(deps): bump astro to 5.2
```

- Scope is optional but useful in multi-app repos — use the app/package name (`dispatch`, `outreach`, `fileflows`).
- Body (if needed): explain *why*, not *what* — the diff already shows what changed. Wrap at ~72 chars.
- Breaking changes: `feat(api)!: change lead payload shape` + a `BREAKING CHANGE:` footer explaining the migration.
- Never bundle unrelated changes into one commit — split by concern even if it means more commits before a PR.

### Committing — before you commit
- Run the project's lint/format/typecheck scripts if they exist (`npm run lint`, `npm run typecheck`) before committing — don't commit code that fails CI checks you could catch locally.
- Check `git status` and `git diff` for accidental inclusions (`.env`, build artifacts, `node_modules`) before staging.
- Stage intentionally (`git add <specific files>`) rather than `git add -A` on repos with generated/build output unless a `.gitignore` is confirmed to cover it.

### Pull requests
Title: same convention as commit summary line.

Description template:
```markdown
## Summary
What changed and why, in 2-3 sentences.

## Changes
- Bullet list of notable changes

## Testing
How this was verified (manual steps, screenshots for UI changes, etc.)
```

- Keep PRs scoped to one concern — large multi-purpose PRs are harder to review and riskier to merge. If a task naturally splits (e.g. backend + frontend), consider separate PRs.
- Link related issues with `Closes #123` / `Fixes #123` in the description so they auto-close on merge.
- For UI changes, note that a screenshot or short clip belongs in the PR — call this out even though the agent can't capture it itself.

### Merge conflicts
- Prefer rebase over merge for feature branches to keep history linear, unless the project's convention is merge commits (check recent history first — don't impose a convention that isn't already there).
- When resolving, re-read both sides of the conflict for *intent*, not just take one side — especially in config/lockfiles where both changes may need to coexist.
- After resolving, re-run tests/build before pushing — a conflict resolution that "looks right" can still break the build.

### `gh` CLI shortcuts worth knowing
- `gh pr create --fill` — creates a PR using the branch's commits as title/body scaffold (edit after).
- `gh pr view --web` — open the PR in browser for final review.
- `gh pr checks` — check CI status without leaving the terminal.
- `gh issue create` — for tracking follow-up work discovered mid-task rather than scope-creeping the current PR.

### Common mistakes to catch
- Commit messages like "fixes" or "wip" with no context — always describe the actual change.
- Force-pushing to a shared branch without checking if others have pulled it.
- Committing directly to `main`/`master` on a project that otherwise uses PRs — flag this even if the person asks for it, and confirm it's intentional.
