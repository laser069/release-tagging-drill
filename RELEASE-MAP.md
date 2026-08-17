# Release Map

Maps each clean semver tag (created per `VERSIONING.md`) to its commit and what actually shipped. This is the source of truth for "what's in production" and "what do I roll back to" — the pre-existing tags (`v1.4.2`, `release_2`, etc., see `TAG-AUDIT.md`) are left in place for history but are not used for this map.

| Version | Commit | Date | What Shipped |
|---|---|---|---|
| v1.0.0 | ff1fb9a | 2026-06-06 | Baseline scaffold: README, CHANGELOG, docs/ (deployment-history, incident-log, release-notes-old), and the placeholder checkout service in src/. |
| v1.0.1 | 28bd110 | 2026-06-06 | Patch: removed stray backticks around list items in docs/deployment-history.md. No behavior change. |
| v1.1.0 | 82535d9 | 2026-06-06 | Minor: refactored src/app.js — SERVICE_NAME/SERVICE_VERSION constants and a run() function replace the old VERSION/runCheckout() names. Backward compatible. Currently on main HEAD. |

Rollback traceability walkthrough is in `TRACEABILITY.md`.
