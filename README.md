# Interlisp Shared Workflows

Organization-wide reusable GitHub Actions workflows for the Interlisp
organization.  Storing them here lets every repository build and deploy the
same way, so updates take effect everywhere at once and no build logic is
duplicated across repositories.

## Repository layout

```
.github/workflows/          reusable workflows (on: workflow_call)
```

## Workflows

### `build-site.yml`

Reusable workflow that builds the Interlisp.org website:

- Checks out a repository/ref (defaults to the caller)
- Queries the Zotero bibliography version and caches/rebuilds the
  bibliography on a version change (cache miss)
- Installs Hugo Extended (version passed as an input)
- Installs Node.js dependencies
- Builds the site with `hugo --cleanDestinationDir -e <environment>`
  (optionally with a `--baseURL` override for per-PR preview subdirectories)
- Optionally runs the full test suite (content-integrity, JSON-LD, and
  build-integrity tests)
- Optionally skips the build when the bibliography is fresh
  (`skip-if-fresh`, used by scheduled production runs) and reports it via the
  `skipped` output
- Optionally uploads the built site as a GitHub Pages artifact

### Referencing the workflows

From any repository in the organization:

```yaml
jobs:
  build:
    uses: Interlisp/shared-workflows/.github/workflows/build-site.yml@main
    with:
      hugo-version: 0.156.0
      hugo-environment: production
```

Reusable workflows can be referenced by **branch, tag, or SHA**. For
stability, pin to the **default branch** (`main`) or a tag once the workflow
is merged; a branch ref is fine during development.