# dozer-ai/.github

Workflows shared across dozer-ai repos. Public so that public repos
(zed-gstreamer-dozer) can call them; GitHub does not let a public repo call a
reusable workflow from a private one. Nothing here may hold or print a secret.

| workflow | what it does | call it with |
|---|---|---|
| `auto-assign.yml` | assigns a new PR to whoever opened it | see below |

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
