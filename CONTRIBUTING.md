# Contributing to loupe-workflows

This repository is the reusable review workflow that `loupe` and its siblings call. It is workflow YAML and nothing else, which is what most of the rules below follow from.

## Setup

`mise install` gets actionlint and shellcheck at the versions `mise.toml` pins, which are the versions `ci.yml` runs in Docker. A local pass therefore means the same thing CI's does.

## Tests

`mise run lint` is the entire test suite. actionlint is all there is to run against workflow YAML, and it shells out to shellcheck for every `run:` block, which is where the real work happens here. A change MUST pass it before it is committed.

## Commits and pull request titles

Both MUST follow [Conventional Commits](https://www.conventionalcommits.org): `type(scope): summary`, with a lowercase summary, at most 72 characters and no trailing period. The summary MUST be imperative ("add", not "added"), and a body MUST be separated from the subject by a blank line. The type is one of `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore` or `revert`. The scope is the area touched: `review`, `ci`, `readme`, `deps` and so on.

The pull request title matters most, because every merge strategy here takes it as the subject of the commit that lands on `main`. That subject is permanent; the branch's own subjects may not survive the merge.

The `pr title` workflow checks what a machine can check: the type, the 72-character cap, the lowercase summary and the absence of a trailing period. It skips Dependabot, the standing exception, since Dependabot prefixes `build` but capitalizes its summary. Nothing checks the imperative mood or the commits on the branch — there is no `lefthook.yml` here as there is in `loupe` — so those are caught only by whoever is reading. The titles merged before this file was written do not follow the convention and are not being rewritten.

## Releases

release-please cuts them. A merge to `main` opens or updates a release pull request; merging that one tags the version, writes `CHANGELOG.md` and publishes the GitHub Release. The version comes from the conventional commit subjects since the last tag, which is the other reason the pull request title matters: a `feat` takes the minor, a `fix` takes the patch, and anything else takes nothing. A commit body carrying `Release-As: <version>` overrides that, which is how a release gets cut for a change that would not otherwise bump.

An agent MAY merge a release pull request. An agent MUST NOT create or move a tag, edit `.release-please-manifest.json` outside a release pull request, or publish a release any other way.

Callers do not pin a tag. They pin the release commit's SHA with the tag beside it in a comment, `review.yml@<release-sha> # <tag>`, which is what the README explains. The README's own copy-paste example still pins a tag, because that is the right thing to hand someone setting this up for the first time.
