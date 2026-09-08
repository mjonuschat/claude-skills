---
name: initializing-project-repos
description: Use when setting up a brand-new project directory or repo from scratch — git init, choosing a license, README/AGENTS.md scaffolding, commit/push conventions, or code formatting standards — before writing any project code or making a commit.
---

# Initializing Project Repos

## Overview

Personal checklist for bootstrapping a new project directory. Covers the
parts of "just set it up" that are actually judgment calls, not defaults to
guess silently — plus the commit/push and formatting conventions that apply
once code starts landing.

## Checklist

1. **git init** — only if not already a repo. Check `git status`/`.git`
   first; don't clobber existing history.
2. **License — always ask.** Never guess, not even MIT. Ask which
   license(s); dual-licensing (e.g. MIT/Apache-2.0) is common for permissive
   libraries that want copyleft consumers without inheriting their
   restrictions.
3. **Ignore rules — prefer `.git/info/exclude` over a tracked `.gitignore`.**
   Local-only, untracked, invisible to `git status` and to a fresh clone.
   Use a tracked `.gitignore` only when the user says the ignores need to
   travel with the repo (e.g. real outside contributors relying on shared
   rules).
4. **README.md — title + one-line description only.** Don't pad it with
   placeholder Installation/Usage/Contributing headers that aren't real yet
   — empty scaffolding invites bit-rot. Expand as the project actually grows.
5. **AGENTS.md / CLAUDE.md** — write project content into `AGENTS.md`;
   `CLAUDE.md` is a one-line include: `@AGENTS.md`. Create both even at
   zero-code bootstrap. **Add both to `.git/info/exclude`** — they're local
   working notes, not tracked project files, same as `.codex/`, `.claude/`,
   and `docs/superpowers/` (specs/plans). None of these get committed.
6. **Repo stays local until a remote is explicitly requested.** Don't run
   `gh repo create`, add a remote, or otherwise reach for a hosted repo
   unless asked. This is separate from push approval (below) — creating the
   remote at all is its own ask.

## Commit & Push Discipline

- **Commit as checkpoints without prompting.** Unlike generic assistant
  defaults, this skill grants standing permission to commit locally as work
  lands — no need to ask each time.
- **Pushing to any remote always requires explicit, per-push approval.**
  Local commits are free; `git push` (including `--force`/
  `--force-with-lease`), `gh release create`, `gh repo edit`, and other
  remote-mutating commands are not. Ask every time.
- **Fixing an unpushed commit means amending/squashing it, not stacking a
  new `fix: ...` commit on top.** `git commit --amend` or an interactive
  rebase against the commit being corrected. Once something is pushed, it's
  no longer "unpushed" — fix it with a new commit instead of rewriting
  shared history.
- **Conventional Commits format:** `<type>(<scope>): <description>`.
  - Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`,
    `build`, `ci`, `chore`, `revert`.
  - **Small projects skip scopes** — `fix: Gate steps during restore`, not
    `fix(foo): ...`. Ask if it's unclear whether a project counts as
    "small" (roughly: single component/package, one repo, no submodules).
    Larger/multi-package repos may use a scope naming the package or
    subcomponent.
  - Imperative mood, sentence case, no period, subject ≤72 chars.
  - **Always include a body** (after a blank line) explaining what/why,
    wrapped at ~100 chars. Subject-only commits aren't compliant.
  - Breaking changes use a `BREAKING CHANGE:` footer, not `feat!:`/`fix!:`.
  - No ticket/issue IDs in the commit message — state the change itself.
  - **Never** add `Co-Authored-By` lines for AI tools or non-human
    contributors.

## Code Formatting Standards

**Rust:**
- `cargo fmt` defaults (no custom `rustfmt.toml` unless the project needs
  one) — 4-space indent, never tabs, max 100 char lines.
- Naming: `snake_case` (functions/variables/modules), `PascalCase`
  (types/traits), `SCREAMING_SNAKE_CASE` (constants). Follow the Rust API
  Guidelines.
- No emoji/unicode-emoji in code or output.
- Doc comments on public items.
- Single responsibility; prefer borrowing (`&T`/`&mut T`) over ownership;
  ≤5 params (config struct beyond that); return early; iterators over
  manual loops; private fields by default; derive `Debug`/`Clone`/
  `PartialEq` where sensible; `unsafe` only with a `// SAFETY:` comment.
- No wildcard imports except preludes and `use super::*` in tests.
- Pre-commit gate: `cargo build && cargo clippy -- -D warnings && cargo fmt --all -- --check && cargo test`.

**Python:**
- `ruff format` + `ruff check` for formatting/linting, `pytest` for tests.

**Testing (both languages):**
- No defensive-noise tests — cover real behavior/contracts/invariants, not
  boilerplate guards or type-system facts.
- No tests for throwaway/experimental tooling; verify those by direct run
  or review instead.
- Arrange-Act-Assert pattern; no commented-out tests.

**General:**
- Never commit commented-out code, `dbg!`/`println!`/debug prints, or
  credentials/secrets.

## Quick Reference

| Decision | Default |
|---|---|
| git init | Only if not already a repo |
| License | Ask — never guess |
| Ignore file | `.git/info/exclude`, not tracked `.gitignore` |
| README | Title + one-liner, no placeholder sections |
| AGENTS.md/CLAUDE.md | AGENTS.md has content, CLAUDE.md = `@AGENTS.md`; both git-excluded |
| Specs/`.codex`/`.claude` | Written to disk, excluded via `.git/info/exclude`, never committed |
| Remote | Never created/added unless explicitly requested |
| Local commits | Checkpoint freely, no need to ask |
| Push | Always requires explicit per-push approval |
| Fixing unpushed commits | Amend/rebase, don't stack `fix:` commits |
| Commit format | Conventional Commits; no scope for small projects; body always required |
| Rust fmt/lint | `cargo fmt`/`clippy -D warnings`, 100 char lines, 4-space indent |
| Python fmt/lint | `ruff format` + `ruff check`, `pytest` |

## Common Mistakes

- Guessing MIT "because that's the default for open source" — ask instead.
- Using a tracked `.gitignore` "because that's normal" — default to the
  local exclude unless shared ignores are actually needed.
- Committing AGENTS.md/CLAUDE.md/specs as tracked files — they belong in
  `.git/info/exclude`.
- Treating "don't commit without asking" as still active here — this skill
  grants standing permission for local checkpoint commits; only pushing
  needs per-instance approval.
- Adding a `fix: small correction` commit on top of an unpushed commit
  instead of amending it.
- Adding a scope to every commit out of habit, even on a small
  single-component project.
- Padding README with empty Installation/Usage/Contributing headers before
  there's anything real to put in them.
