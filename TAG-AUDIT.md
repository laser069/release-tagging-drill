# Tag Audit

Audit of the existing tag history on `main`, done by running `git tag`, `git log --oneline --decorate --all`, and `git tag --sort=-v:refname` against the repo as forked. Findings below are specific tags with specific evidence, not general complaints.

## 1. `release_2` exists with no `release_1` anywhere in history

`git tag` shows `release_2` but no `release_1`, `release-1`, or anything earlier in that scheme. There's no way to tell if a first release was never tagged, was deleted, or if the numbering just started at 2 by mistake. The incident log confirms this bit the team for real: on 2026-04-22 they rolled back to `release_2` after a payment gateway regression, and the rollback failed because `release_2` is a lightweight tag pointing to commit `28bd110` with no annotation tying it to a known-good state. A number-only sequence with a gap is worse than no sequence, because it implies an order that doesn't actually exist.

## 2. `v2-final-FINAL` and `patch-new` point to the same commit (`82535d9`)

Two different tag names for the identical commit, with no indication which one is authoritative. `v2-final-FINAL` isn't valid semver (the "-final-FINAL" suffix isn't a real pre-release identifier, it's just someone re-tagging because they weren't confident the first one was really final), so `git tag --sort=-v:refname` can't place it correctly relative to `v1.4.2` or `1.5.0` — it's a string, not a version. If two people on the team each know the deployment by a different one of these two names, they'll think they're talking about different releases when they're talking about the same code.

## 3. `v1.4.2` and `stable-build` also point to the same commit (`ff1fb9a`)

Same duplicate-tag problem as above, but here it's compounded by `stable-build` being a floating, non-versioned label — the deployment history doc lists a 2026-03-15 deployment tagged `stable-build` with "missing deployment record," and the incident log calls out `stable-build` and `patch-new` by name as tags that "did not follow any standard." A label like `stable-build` carries no ordering information: six months from now nobody can tell if it's older or newer than any other tag without manually diffing commits.

## 4. `release_2` and `version-1.0` also point to the same commit (`28bd110`)

Two more tags on one commit, this time mixing a `release_N` scheme with a `version-X.Y` scheme for the exact same code. This is the same commit the team rolled back to in Incident 1 and got burned by. If someone reads the old release notes and sees `version-1.0` described as "first release" and separately sees `release_2` described as "minor changes and rollback candidate," they'd reasonably conclude these are two different points in history — they aren't. Any script or person picking "the" tag for that commit has to guess which name is canonical.

## 5. `latest-good` and `release_2`/`version-1.0` have byte-identical commit messages but different commits

`latest-good` points to `da6b2ed`, whose commit message is `docs: add broken deployment history and incident log` — the exact same message as `28bd110`, which is tagged `release_2`/`version-1.0`. `git log` alone cannot distinguish these two commits by message; you have to already know to compare hashes. Combined with the fact that `latest-good` is an annotated tag with nothing in Git stopping someone from deleting and re-creating it to point somewhere else later (annotated doesn't mean immutable, just that it has metadata), this tag name is actively misleading: it asserts a property ("this is good") without any CI, test, or review record backing that claim, and the underlying commit it points to is easy to confuse with a different commit carrying the same description.

## 6. Lightweight tags carry no message, tagger, or date

`v1.4.2`, `release_2`, `version-1.0`, `v2-final-FINAL`, and `patch-new` are all lightweight tags — they're just a name pointing at a commit, nothing else. `git show v1.4.2` gives you the commit, not a release message, so there's no record of who cut the release, when it was tagged (as opposed to when the commit was authored), or why. `1.5.0`, `stable-build`, and `latest-good` are the only annotated tags in the history, and even those don't follow the `v` prefix used elsewhere (`1.5.0` vs `v1.4.2`), so a simple `git tag -l "v*"` filter silently misses the newest of the pre-existing semver-ish tags.

## 7. The tag set doesn't sort into anything usable

Running `git tag --sort=-v:refname` on the existing tags produces: `version-1.0`, `v2-final-FINAL`, `v1.4.2`, `stable-build`, `release_2`, `patch-new`, `latest-good`, `1.5.0` — an order that has nothing to do with either commit chronology or actual release sequence (the real chronological order, oldest to newest, is `v1.4.2`/`stable-build` → `release_2`/`version-1.0` → `latest-good` → `1.5.0` → `v2-final-FINAL`/`patch-new`). This is the whole point of using `--sort=-v:refname` for rollback triage — it's supposed to hand you the latest release without reading history — and on this repo it hands you garbage instead.
