# loupe-review

A reusable GitHub Actions workflow that reviews a pull request with an agent and publishes the result as one GitHub review, using [loupe](https://github.com/eriksaulnier/loupe).

A round captures the pull request, runs Claude Code over the captured head and diff, and publishes what the agent filed. Nothing is posted that loupe did not compose, and the published body passes loupe's Markdown allowlist before it is sent.

## Why a reusable workflow and not an action

The agent never holds a token that can write to the pull request.

`claude-code-action` embeds the token it is given into the workspace's `.git/config` before it starts the agent, and a review is exactly the place untrusted text gets read. So the round is split across two jobs: `review` runs the agent with a read-only token, hands its data root on as an artifact, and `publish` posts from that artifact with a write token and no agent, no checkout and no pull request content in its workspace.

A composite action cannot do this. A composite action runs inside the job that calls it — one job, one token, the agent holding it. That is why this ships as `workflow_call`.

## Using it

```yaml
name: review

on:
  workflow_dispatch:
    inputs:
      pull_request:
        description: Pull request number to review
        required: true
        type: number
  pull_request:
    types: [opened, ready_for_review, labeled]

jobs:
  review:
    # The kill switch lives in the repository it guards. Flip REVIEW_ENABLED to "true" to let a
    # round run at all.
    if: vars.REVIEW_ENABLED == 'true'
    uses: eriksaulnier/loupe-review/.github/workflows/review.yml@v1
    # A called workflow's job permissions are capped by the calling job's. Read the gotcha below
    # before changing these.
    permissions:
      contents: read
      pull-requests: write
      checks: read
      actions: read
    with:
      pull_request: ${{ inputs.pull_request || github.event.pull_request.number }}
      model: ${{ vars.REVIEW_MODEL }}
      debug: ${{ vars.REVIEW_DEBUG == 'true' }}
    secrets:
      openrouter_api_key: ${{ secrets.OPENROUTER_API_KEY }}
```

## The caller permissions gotcha

**The caller MUST grant `contents: read`, `pull-requests: write`, `checks: read` and `actions: read` on the calling job.**

A called workflow's per-job `permissions:` are a ceiling, not a grant: the token each of its jobs gets is the intersection of what the job asks for and what the *calling* job was given. Leave them off and the calling job falls back to the repository's default — which, in most repositories, is read-only. The `review` job still runs, the agent still files findings, and then `publish` gets a read-only token and fails at the last step with a permissions error that names loupe rather than your workflow.

Granting them on the calling job does not hand the agent a write token. The split is inside this workflow: `review` asks for `pull-requests: read` and gets no more, whatever the caller allows.

## Inputs

| Input | Type | Default | What it does |
| :--- | :--- | :--- | :--- |
| `pull_request` | number | *required* | The pull request to review. |
| `model` | string | *required* | The OpenRouter model id the agent runs on. |
| `loupe_version` | string | `v0.7.0` | Tag of the loupe release both jobs install. The archive's sha256 is checked against the release's `checksums.txt`. |
| `label` | string | `ai-review` | The label that asks for a round, and that the `unlabel` job takes back off. |
| `source` | string | `loupe-review` | The source name loupe records, which appears in the published review's footer. |
| `debug` | boolean | `false` | Show the agent's full output in the job log. The log is as public as the calling repository, so leave it off unless you are diagnosing a run. |
| `max_turns` | number | `40` | Turn limit for the agent. |
| `instructions_path` | string | `.github/review-instructions.md` | Path, **in the default branch**, to this repository's own review instructions. |
| `wait_for_checks` | number | `10` | Minutes to wait for the repository's other checks to settle before reviewing an automatic round. `0` disables the wait. |

### Secrets

| Secret | Required | What it is |
| :--- | :--- | :--- |
| `openrouter_api_key` | yes | The OpenRouter key the agent authenticates with. The agent is Claude Code pointed at OpenRouter's Anthropic-compatible endpoint. |

## When a round runs

- `workflow_dispatch` naming a pull request.
- `opened` and `ready_for_review` — the moments a pull request first asks to be read.
- `labeled` with `inputs.label`. This is how a round is asked for *again*: take the label off and put it back. The `unlabel` job removes it once the round has answered, including when the answer was a failure.

A round is skipped, without failing, when the pull request comes from a fork. GitHub hands a fork's `pull_request` event a read-only token whatever the job asks for, so publishing is impossible whoever asked.

An automatic round is also skipped for a draft, a `release-please--*` branch, or an author ending in `[bot]`. A label or a dispatch overrides all three: asking by hand means you want it reviewed anyway.

An automatic round waits for the repository's other checks to settle first, up to `wait_for_checks` minutes, so the agent is told what the build and the linters already concluded rather than guessing. A round asked for by hand does not wait: whoever added the label or ran the dispatch decided the pull request was ready to read.

## Per-repository review instructions

The base prompt covers what is structural — where the diff and the captured head are, what a `blocking` finding MUST rest on, how to file through `findings.json`. What it does not know is your repository: which linters already run, which packages are contracts, what a reviewer SHOULD leave alone.

Put that in `instructions_path`. The file is read from the **default branch**, never the pull request head, so a pull request cannot rewrite the instructions that judge it. Its contents are appended to the prompt as one section, under a random heredoc delimiter so nothing in the file can break out and set other environment variables. A missing file is not an error — the base prompt runs alone.

## Manual setup, per repository

None of this is created for you:

- **The `ai-review` label.** Create it (or whatever you set `label` to). A round asked for by hand needs it to exist before it can be added.
- **The `OPENROUTER_API_KEY` secret.** Set it on the repository or the organization and pass it through as `openrouter_api_key`.
- **`REVIEW_ENABLED`.** A repository variable, `"true"` to let rounds run. The kill switch lives in the repository it guards, so it is a caller-side `if:` and not an input here. Nothing runs until you set it.
- **`REVIEW_MODEL`.** A repository variable holding the OpenRouter model id, passed in as `model`. Keeping it a variable means changing models without touching the workflow.
- **`REVIEW_DEBUG`.** A repository variable, `"true"` to pass `debug: true`. The agent's log is as public as the repository.

## License

MIT.
