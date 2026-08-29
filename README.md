# Bhitti Documentation

This directory contains the current developer documentation for the Bhitti PHP framework and starter application.

The documentation is aligned with the current Bhitti application and `sayedsahin/bhitti-framework` code after the framework `v0.1.0` baseline.

## Local Jekyll preview

This documentation keeps the existing Jekyll structure (`_config.yml`, `_layouts/`, `_data/navigation.yml`, `assets/`). Publish it with GitHub Pages or your normal Jekyll workflow.

## Documentation rules

- Current source code is the API source of truth.
- Use `php run` for CLI examples.
- Use `resources/views/*.view.php` for views.
- Keep kernel middleware stateless; session-aware middleware belongs to matched routes.
- Document raw SQL APIs as developer-controlled escape hatches.
