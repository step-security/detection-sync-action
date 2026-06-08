[![StepSecurity Maintained Action](https://raw.githubusercontent.com/step-security/maintained-actions-assets/main/assets/maintained-action-banner.png)](https://docs.stepsecurity.io/actions/stepsecurity-maintained-actions)

# RunReveal Detection Sync

Synchronizes local detection rule files with a RunReveal workspace using the RunReveal CLI.

## Usage

The example below shows a common pattern: validate on pull requests with a dry run, then apply on merge to `main`.

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  sync-detections:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - uses: actions/checkout@v4

      - name: Dry-run on pull request
        if: github.event_name == 'pull_request'
        uses: step-security/detection-sync-action@v1
        with:
          token: ${{ secrets.RUNREVEAL_TOKEN }}
          workspace: ${{ vars.RUNREVEAL_WORKSPACE }}
          directory: ./rules
          dry-run: true
          github-token: ${{ secrets.GITHUB_TOKEN }}

      - name: Apply detections on merge
        if: github.event_name == 'push'
        uses: step-security/detection-sync-action@v1
        with:
          token: ${{ secrets.RUNREVEAL_TOKEN }}
          workspace: ${{ vars.RUNREVEAL_WORKSPACE }}
          directory: ./rules
```

## Inputs

| Name | Required | Default | Description |
|---|---|---|---|
| `token` | Yes | — | API token for authenticating with RunReveal. Must have permission to manage detections. |
| `workspace` | Yes | — | Unique identifier of the RunReveal workspace that will receive the detection updates. |
| `directory` | Yes | `./` | Path to the local directory containing detection files to synchronize. |
| `dry-run` | No | `false` | When set to true, validates detections without applying changes. Useful for PR checks. |
| `github-token` | No | `${{ github.token }}` | Token used to post PR comments when a dry-run validation fails. Defaults to the workflow token. |

## Outputs

This action does not produce any outputs.
