# Security Policy

## Reporting a Vulnerability

Please report security issues privately using
[GitHub Security Advisories](https://github.com/teerakarna/mkdocs-publisher/security/advisories/new)
for this repository, rather than opening a public issue.

If you're unable to use Security Advisories, email 21040807+teerakarna@users.noreply.github.com
with a description of the issue and steps to reproduce.

Please do not disclose the issue publicly until it has been addressed.

## Scope

This repo is a single `workflow_call` reusable workflow. It runs *inside the calling repo's own
CI job*, with whatever permissions that caller's workflow grants it - so a compromised version of
this workflow is a supply-chain vector for every repo that calls it, not just this one. The main
areas of security interest:

- **The `publish.yml` reusable workflow itself** - every step it runs, every action it calls, and
  every input it accepts. It builds and deploys exactly what the caller's `mkdocs.yml` and
  `requirements.txt` produce; it never fetches or executes anything from outside the calling
  repo's own tree.
- **Third-party actions this workflow depends on** - all pinned to a commit SHA, never a floating
  tag or branch. A Dependabot-proposed bump to one of these is a security-relevant change, not a
  routine one - review the diff, not just the version number.
- **The permissions this workflow requests** - `contents: read`, `pages: write`, `id-token: write`
  on the caller's side. Any change proposing broader permissions than that needs a stated reason.

## What's out of scope

Anything in a *calling* repo's own `mkdocs.yml`, theme choice, or docs content - this repo has no
visibility into and no responsibility for what a caller chooses to publish.
