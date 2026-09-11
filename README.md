# mkdocs-publisher

A reusable GitHub Actions workflow that builds an [MkDocs](https://www.mkdocs.org/) site with
`mkdocs build --strict` and deploys it to the *calling* repo's own GitHub Pages.

**Theme-agnostic.** The workflow never installs or assumes a theme - it just runs `mkdocs build`
against whatever `mkdocs.yml` and `requirements.txt` the calling repo provides. Pin
[Material](https://squidfunk.github.io/mkdocs-material/), the built-in `readthedocs`/`mkdocs`
themes, or any other MkDocs theme in *that repo's own* `requirements.txt` and `mkdocs.yml` -
this workflow builds it exactly the same way either way.

This repo holds the mechanism only. It never hosts content, and it isn't a template to copy -
any repo, in any org, calls it directly and keeps full ownership of its own docs, its own theme
choice, and its own Pages site.

## Usage

1. Give the calling repo an MkDocs site (`mkdocs.yml`, a `requirements.txt` pinning whichever
   theme you want - e.g. `mkdocs-material` - and a `docs/` directory) wherever you like in its
   tree.
2. Enable GitHub Pages on that repo with source **GitHub Actions**:
   ```sh
   gh api -X POST repos/<owner>/<repo>/pages -f build_type=workflow
   ```
   (one-time; Settings → Pages → Source works too)
3. Add a workflow that calls this one:

   ```yaml
   name: Publish docs

   on:
     push:
       branches: [main]

   permissions:
     contents: read
     pages: write
     id-token: write

   jobs:
     publish:
       # Pin to a commit SHA, not @main - same discipline you'd want for any third-party
       # action. Find the current one with: git ls-remote https://github.com/teerakarna/mkdocs-publisher main
       uses: teerakarna/mkdocs-publisher/.github/workflows/publish.yml@<commit-sha> # main
       with:
         working-directory: docs-site   # optional - defaults to repo root
   ```

   The `permissions` block above is required on the *calling* workflow - a reusable workflow can
   only use permissions the caller actually grants it.

## Inputs

| Input | Default | Meaning |
|---|---|---|
| `working-directory` | `.` | Directory containing `mkdocs.yml`, relative to the calling repo's root. |
| `requirements-file` | `requirements.txt` | Pip requirements file, relative to `working-directory`. |
| `python-version` | `3.13` | Python version to build with. |

## Why this exists

Every project's docs deploy is the same five steps (checkout, install, build strict, upload,
deploy) copy-pasted with minor drift each time. This pins that pipeline in one place, SHA-pinned
and verified, so a project's own workflow file is three lines instead of thirty - and a security
fix to the pipeline lands everywhere that calls it, once.

## License

Apache-2.0 - see [LICENSE](LICENSE).
