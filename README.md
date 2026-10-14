# dozer-ai/.github

Workflows shared across dozer-ai repos. Public so that public repos
(zed-gstreamer-dozer) can call them; GitHub does not let a public repo call a
reusable workflow from a private one. Nothing here may hold or print a secret.

| workflow | what it does |
|---|---|
| `auto-assign.yml` | assigns a new PR to whoever opened it |
| `code-review.yml` | an advisory Claude review of each non-draft PR, at most 3 automatic rounds |

## auto-assign

```yaml
# .github/workflows/auto-assign.yml in the calling repo
name: auto-assign
on:
  pull_request:
    types: [opened, reopened]
jobs:
  auto-assign:
    uses: dozer-ai/.github/.github/workflows/auto-assign.yml@main
    permissions:
      pull-requests: write
```

## code-review

The calling repo keeps the triggers, the paths not worth reviewing, and its own
prompt; see star_system's `.github/workflows/code-review.yml` for a full stub.

```yaml
jobs:
  review:
    uses: dozer-ai/.github/.github/workflows/code-review.yml@main
    permissions:
      contents: read
      pull-requests: write
      id-token: write
    with:
      pr_number: ${{ format('{0}', github.event.pull_request.number || inputs.pr_number) }}
      prompt: |
        Review this pull request. The repository is ...
```

**No secret to set.** The workflow reaches Claude through Workload Identity
Federation: it trades the run's GitHub OIDC token for a Claude token that lasts
minutes. The Anthropic rule `gha-code-review` (Console → Settings → Workload
identity) accepts a token only when all of these hold:

* the repository owner is `dozer-ai`, by name and by numeric ID `84637224`;
* the event is `pull_request` or `workflow_dispatch`;
* the job runs `dozer-ai/.github/.github/workflows/code-review.yml@refs/heads/main`.

So only this file, as merged to `main`, can get a token. Renaming or moving it
breaks that match: change the rule in the same breath. The tokens act as the
service account `github-code-review` in the `github-ci` workspace, whose spend
limit caps the cost.
