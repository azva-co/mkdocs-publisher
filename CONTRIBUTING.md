# Contributing

This is a small, single-purpose reusable workflow - the whole surface area is `publish.yml` and
its `workflow_call` inputs. Keep changes scoped to that; this repo deliberately doesn't grow into
a general static-site builder (see the README's "theme-agnostic" framing - it stays MkDocs-only
and GitHub-Pages-only by design).

## Testing a change

There's no meaningful unit test for a reusable workflow - the real test is calling it from an
actual repo with a real MkDocs site and watching the run. Before opening a PR:

1. Point a real caller (or a throwaway test repo) at your branch:
   `uses: <you>/mkdocs-publisher/.github/workflows/publish.yml@<your-branch-or-sha>`
2. Trigger it via `workflow_dispatch` on a non-default branch first if you want to check the
   build without risking a deploy - GitHub Pages environments only allow the default branch to
   deploy by policy, so a manual dispatch on a feature branch will build but correctly fail at
   the deploy step. That's expected, not a bug in your change.
3. Confirm the real push-triggered run on `main` (or the caller's default branch) succeeds end to
   end, and that the published site is actually reachable - a green deploy step isn't sufficient
   proof on its own.

## Conventions

- Conventional commits (`feat:`, `fix:`, `chore:`, `docs:`) where practical.
- Every third-party action pinned to a full commit SHA, never a tag or branch - `zizmor` (in
  `lint.yml`) enforces this.
- Branch + PR for everything, squash merge. No direct pushes to `main`.
- A change to `publish.yml`'s inputs (adding, removing, or changing the meaning of one) is a
  breaking change - bump the major version tag and say so in the PR.
