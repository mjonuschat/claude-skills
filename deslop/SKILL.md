---
name: deslop
description: Use when the user asks to remove AI code slop, clean up code style, or run a deslop pass — checks the current diff against main for unnecessary comments, unwarranted defensive code, and other patterns that don't match the surrounding codebase.
---

# Remove AI code slop

Check the diff against `main` (including the uncommitted working tree) and
remove AI-generated slop introduced in the branch.

This is a lighter, broader pass than a dedicated comment audit. For a
thorough comment-only sweep, use the `no-comments` skill instead — the two
compose fine, in either order.

## Focus areas

- Extra comments that are unnecessary or inconsistent with local style.
- Defensive checks, fallbacks, or error handling for states that can't occur
  on a trusted internal path — an unwarranted `try`/`except`, an `Option`
  branch for a value that's always `Some`, a `Result` arm for an error the
  caller already ruled out.
- Casts or suppressions used only to bypass a real issue instead of fixing
  it: an `as` cast papering over a type mismatch, `#[allow(...)]` /
  `#[cfg_attr(..., allow(...))]` silencing a lint that's actually right,
  Python's `# type: ignore` / `# noqa` doing the same.
- Deeply nested code that should be simplified with early returns.
- Other patterns inconsistent with the file and surrounding codebase.

## Guardrails

- Keep behavior unchanged unless fixing a clear bug.
- Prefer minimal, focused edits over broad rewrites.
- Don't relitigate a project convention already settled in `AGENTS.md` —
  apply it, don't argue with it.
- Keep the final summary concise (1-3 sentences).
