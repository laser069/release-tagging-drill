# Versioning Convention

This is the policy going forward, replacing the ad-hoc tagging documented in `TAG-AUDIT.md`.

## Semantic versioning rules

Versions are `MAJOR.MINOR.PATCH`.

- **MAJOR** — breaking change, incompatible with the previous version. Example: `v1.4.2 -> v2.0.0` if the checkout service's API response shape changes in a way older clients can't parse.
- **MINOR** — new functionality, backward compatible. Example: `v1.4.2 -> v1.5.0` for adding audit logging without changing any existing behavior.
- **PATCH** — backward-compatible bug fix, no new functionality. Example: `v1.4.2 -> v1.4.3` for fixing the checkout timeout bug without touching the API.

## Tag naming format

`vMAJOR.MINOR.PATCH` — always with the `v` prefix, no other text appended (no `-final`, no `-FINAL`, no branch names). Example: `v1.2.0`.

This replaces the mixed schemes found in the existing history (`release_2`, `version-1.0`, `stable-build`, `patch-new`) with one format everyone uses.

## Annotated vs lightweight tags

Releases use **annotated** tags, always. An annotated tag stores the tagger, date, and a message — a lightweight tag is just a pointer to a commit with none of that. The existing history's worst tags (`v1.4.2`, `release_2`, `version-1.0`, `v2-final-FINAL`, `patch-new`) are all lightweight, which is exactly why nobody could tell who cut them or why. Command:

```
git tag -a vMAJOR.MINOR.PATCH -m "Release MAJOR.MINOR.PATCH: <what changed>"
```

## Pre-release rule

Release candidates and betas are marked `vMAJOR.MINOR.PATCH-rc.N` or `vMAJOR.MINOR.PATCH-beta.N`, e.g. `v1.5.0-rc.1`. Per semver precedence, a pre-release sorts *before* its final release: `v1.5.0-rc.1 < v1.5.0-rc.2 < v1.5.0`. So `git tag --sort=-v:refname` will always list the finished `v1.5.0` above any `v1.5.0-rc.N`, which is what lets `--sort` be used for "what's the real latest release" without pre-releases getting mistaken for it.
