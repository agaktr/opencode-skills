---
name: wordpress-ddev
description: Use when working on WordPress projects in DDEV, WP-CLI, wp db, themes, plugins, users, options, search-replace, imports, exports, or WordPress admin behavior.
---

# WordPress DDEV Standards

Use this skill for WordPress project behavior.

Always load `office-ddev` whenever this skill is used. Use `office-ddev` for `/srv/projects` path mapping, DDEV routing, known project details, and environment-specific safety rules. Use this skill for WordPress-specific workflows such as WP-CLI, themes, plugins, users, options, imports, exports, and admin behavior.

## Command Pattern

Run WordPress commands through DDEV from the project directory.

Set the tool `workdir` to `/srv/projects/<project>` and run:

`ddev wp <command>`

Do not prefix normal WordPress/DDEV commands with `ssh office-dev` and do not chain `cd`; the assistant should already be running in the local Remote-SSH server environment.

Examples:

- `ddev wp plugin list`
- `ddev wp option get siteurl`
- `ddev wp db check`

Prefer `ddev wp ...` over raw PHP, raw MySQL, direct Docker, or browser-only admin changes when a repeatable CLI action exists.

## Database Work

Use WP-CLI database commands for WordPress database work:

- `ddev wp db check`
- `ddev wp db optimize`
- `ddev wp db export <file>`
- `ddev wp db import <file>`
- `ddev wp db query '<sql>'`

Prefer `ddev wp db ...` over `ddev mysql` for WordPress database tasks because WP-CLI runs in the WordPress context and uses the configured database.

Before destructive or broad database operations, make a local DDEV database export unless the user explicitly says not to:

`ddev export-db --file=<file>`

Do not connect directly to production databases. Do not use production credentials unless the user explicitly confirms the exact production action.

Do not expose or commit salts, API keys, SMTP/payment credentials, production URLs, or other secrets from `wp-config.php`, `.env`, database options, or exports.

## Search Replace

Use WP-CLI search-replace instead of ad hoc SQL for serialized WordPress data.

Always dry-run first:

`ddev wp search-replace '<old>' '<new>' --all-tables-with-prefix --dry-run`

Only run the real command after reviewing dry-run output:

`ddev wp search-replace '<old>' '<new>' --all-tables-with-prefix`

Prefer `--all-tables-with-prefix` for normal WordPress tables. Use `--all-tables` only when there is a clear reason to include non-WordPress tables.

## Core, Plugins, And Themes

Never edit WordPress core directly. Change project-owned plugins, mu-plugins, themes, child themes, configuration, or content instead.

Inspect state before changing it:

- `ddev wp core version`
- `ddev wp plugin list`
- `ddev wp theme list`
- `ddev wp user list`
- `ddev wp option get <name>`

For plugin and theme changes, prefer project-owned code over admin-only configuration when the behavior should be versioned.

Do not update core, plugins, or themes with WP-CLI unless the user explicitly asks for dependency updates. If updates are requested, check status first and avoid touching unrelated packages.

## Content And Options

Use WP-CLI for repeatable content and option operations:

- `ddev wp post list --post_type=<type>`
- `ddev wp post get <id>`
- `ddev wp option get <name>`
- `ddev wp option update <name> <value>`
- `ddev wp transient delete --all`
- `ddev wp cache flush`

Be careful with serialized options. Prefer WP-CLI option commands or WordPress APIs over manual SQL updates.

## Verification

After WordPress changes, verify with the smallest relevant checks:

- `ddev wp core is-installed`
- `ddev wp plugin status <plugin>`
- `ddev wp theme status <theme>`
- `ddev wp db check`
- `ddev wp cache flush` when cache may hide the change

When behavior is visible in the browser, also verify the DDEV site URL if practical.
