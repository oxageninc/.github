# DoD workflows

This repository holds the definition-of-done (DoD) check that Oxagen's repositories share. A definition of done is the checklist an issue must have fully ticked before it can close.

| Workflow | What it does |
|---|---|
| `dod-check.yml` | Fails a pull request whose closing issue still has unticked DoD items, or whose body or commits misuse a closing keyword |
| `dod-close-guard.yml` | Reopens an issue closed as completed while its DoD still has unticked items |
| `dod-recheck.yml` | Re-runs a failed `dod` run when someone edits the issue it judged |

The workflows import their logic from this directory:

- `scr-dod-check.mjs` reads issue links and DoD sections.
- `check-closing-keywords.mjs` catches a negated closing keyword, such as "does not close #12", which GitHub still treats as a close.
- `scr-drift-issue.mjs` files and closes the drift issue for `scr-corpus-check` in oxageninc/product.

## Why the check lives here

This repository is public. Any repository can call a public repository's reusable workflow, and any caller's `GITHUB_TOKEN` can check out these scripts. The check lived in oxageninc/product until that repository became private on 2026-10-01. It moved here on 2026-10-02 (oxageninc/product#5183). ADR-039 and ADR-045 in oxageninc/product record the design.

## Callers

oxageninc/product, oxageninc/cgp-website, oxageninc/context-graph-protocol, macanderson/arenabench, and macanderson/stella each carry three short caller files. Each caller pins a commit on this repository's `main`:

```yaml
jobs:
  dod:
    uses: oxageninc/.github/.github/workflows/dod-check.yml@<commit>
```

The scripts are not pinned. A called workflow checks out this repository's `main` for them, because inside a called workflow `github.sha` is the caller's commit.

## Change the check

1. Change the workflow or script here, with a test. The `test` workflow runs the tests, and `dod-check.yml` judges its own pull request.
2. After it merges, re-pin all five callers to the new `main` commit in one sweep. `check-dod-stub-parity.mjs` in oxageninc/product fails while the callers resolve to different workflow files.
