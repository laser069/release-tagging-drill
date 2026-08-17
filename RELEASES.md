# Release Notes

## v1.0.0 — 2026-06-06

Added
- README, CHANGELOG, docs/ (deployment-history, incident-log, release-notes-old), and the placeholder checkout service in src/.

This is the baseline release under the new tagging convention. No prior clean version to roll back to — this is the floor.

## v1.0.1 — 2026-06-06

Fixed
- Stray backticks removed around list items in docs/deployment-history.md. No behavior change.

Rollback target: v1.0.0.

## v1.1.0 — 2026-06-06

Changed
- src/app.js refactored: SERVICE_NAME/SERVICE_VERSION constants and a run() function replace the old VERSION/runCheckout() naming. Backward compatible — same behavior, cleaner structure.

Rollback target: v1.0.1.
