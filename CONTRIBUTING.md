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

Callers pin `v1`, and `v1` is moved by hand, as are the version tags behind it. A merge to `main` therefore reaches no caller until someone moves the tag. An agent MUST NOT move `v1`, create a version tag or cut a release.
