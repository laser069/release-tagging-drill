# Rollback Traceability

Commands a team runs to identify the current and previous releases and roll back to the last known-good tag:

```
git tag --sort=-v:refname        # the sortable release history
git checkout v1.1.0               # current release
git checkout v1.0.1               # roll back to the last known-good tag, then redeploy
```

`git tag --sort=-v:refname` on the new tags returns `v1.1.0`, `v1.0.1`, `v1.0.0` in that order — correct, every time, because they're real semver strings. `v1.1.0` is current (see `RELEASE-MAP.md`), so `v1.0.1` is the last known-good tag to fall back to if `v1.1.0` fails; `git checkout v1.0.1` puts the working tree at commit `28bd110`, and a real deploy would redeploy from that commit.

Compare that to the original history: `git tag --sort=-v:refname` on the pre-existing tags returned `version-1.0`, `v2-final-FINAL`, `v1.4.2`, `stable-build`, `release_2`, `patch-new`, `latest-good`, `1.5.0` — an order with no relationship to when those commits were actually made (see `TAG-AUDIT.md` finding 7). Several of those tags also point to the same commit under different names (`v2-final-FINAL`/`patch-new`, `v1.4.2`/`stable-build`, `release_2`/`version-1.0`), so even after finding "the latest tag" a team would still have to guess which of two names for the same commit was the one anyone else on the team actually meant. That's exactly what happened in Incident 1 in `docs/incident-log.md`: the team rolled back to `release_2` and it turned out to point to an older unsupported commit because nothing tied that tag to a verified deployment record.

The new sequence fixes both problems at once: one canonical tag per commit, real semver so `--sort` orders them correctly, and an annotated message on each tag stating what shipped, so "roll back to the last known-good tag" is a lookup instead of an investigation.
