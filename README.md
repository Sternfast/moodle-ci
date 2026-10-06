# Sternfast/moodle-ci — shared reusable CI for all our Moodle plugins

One reusable GitHub Actions workflow that runs the full `moodle-plugin-ci` suite (phplint, phpmd,
phpcs, phpdoc, validate, savepoints, mustache, grunt, phpunit, behat) across a Moodle-branch × PHP ×
DB matrix. **This is Layer 2 of the plugin factory — the "fix once, all inherit" lever.**

## Use it in a plugin repo
Add `.github/workflows/ci.yml` (copy `ci-caller.yml`):

```yaml
name: CI
on: { push: { branches: [main] }, pull_request: }
jobs:
  moodle-plugin-ci:
    uses: Sternfast/moodle-ci/.github/workflows/plugin-ci.yml@main
```

That's the entire per-plugin CI file. Everything else lives here.

## Maintain it (the payoff)
- **New Moodle release?** Edit the default matrix in `plugin-ci.yml` (one place) → every plugin is
  tested against it on its next build. No per-repo edits.
- **New best-practice step?** Add it here once; all plugins inherit it in lock step.
- Tag releases (`v1`, `v2`, …) and point callers at a tag for stability; bump the tag to roll out.

## Notes
- Default matrix: Moodle 4.5 LTS (PHP 8.2), 5.0 (8.3), 5.3 (8.3). A plugin can pass `matrix-json` to
  override. Keep PHP paired to each branch's minimum.
- A plugin's full declared compatibility still lives in its own `version.php` `$plugin->supported`
  (that's what the Marketplace reads); CI tests representative branches from that range.
- Pattern basis: Catalyst IT runs 100+ plugins on exactly this model
  (`catalyst/catalyst-moodle-workflows`). If we ever want zero-maintenance CI we can point callers at
  their public workflow instead; we keep our own for full control.
