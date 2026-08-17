# Documentation Maintenance

Use the current Bhitti application and framework source as the source of truth.

When updating these docs:

1. Verify public method names and command usage against current source.
2. Check both framework and starter-application changes because configuration/examples often live in the app repository.
3. Keep examples small and executable.
4. Distinguish framework features from starter-application features (for example `Auth`, `Role`, and `flash()` are application support components).
5. Update `upgrade-from-v0.1.0.md` only for changes that are actually present in current code.
6. Keep `_data/navigation.yml` synchronized with added/removed pages.
7. Run a link/filename check before publishing.
