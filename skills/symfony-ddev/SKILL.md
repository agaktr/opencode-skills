---
name: symfony-ddev
description: Use when working on Symfony projects in DDEV, Doctrine, doctrine:migrations, make:migration, bin/console, Messenger, fixtures, cache, tests, Symfony environment configuration, Tailwind, Stimulus, Webpack Encore, or Symfony frontend assets.
---

# Symfony DDEV Standards

Use this skill for Symfony project behavior.

Always load `office-ddev` whenever this skill is used. Use `office-ddev` for `/srv/projects` path mapping, DDEV routing, known project details, and environment-specific safety rules. Use this skill for Symfony-specific workflows such as Console, Doctrine, migrations, Messenger, fixtures, cache, tests, and Symfony assets.

## Command Pattern

Run Symfony commands through DDEV from the project directory.

Set the tool `workdir` to `/srv/projects/<project>` and run:

`ddev exec php bin/console <command>`

Do not prefix normal Symfony/DDEV commands with `ssh office-dev` and do not chain `cd`; the assistant should already be running in the local Remote-SSH server environment.

Examples:

- `ddev exec php bin/console about`
- `ddev exec php bin/console debug:router`
- `ddev exec php bin/console doctrine:migrations:status`

Prefer Symfony Console and Doctrine commands over raw SQL or direct database edits.

## Doctrine And Migrations

For schema changes, use the normal Doctrine migration workflow:

1. Change entities or mapping files.
2. Generate the migration with `make:migration`.
3. Review the generated migration file before running it.
4. Apply it with `doctrine:migrations:migrate`.
5. Validate with `doctrine:schema:validate` when appropriate.

Commands:

- `ddev exec php bin/console make:migration`
- `ddev exec php bin/console doctrine:migrations:status`
- `ddev exec php bin/console doctrine:migrations:migrate`
- `ddev exec php bin/console doctrine:schema:validate`

Do not hand-write new migration files unless the user explicitly asks or generation cannot represent the required data/schema change. If manual edits are needed, keep them minimal and explain why.

Do not use `doctrine:schema:update --force` for normal work. Use migrations instead.

Do not modify migration history tables manually unless explicitly asked and the risk is understood.

## Database Access

Prefer Doctrine-aware commands and project shortcuts over raw MySQL:

- `ddev exec php bin/console doctrine:database:create --if-not-exists`
- `ddev exec php bin/console doctrine:migrations:migrate`
- `ddev exec php bin/console doctrine:query:sql '<sql>'` for targeted inspection only

Use `ddev mysql` only when Symfony or Doctrine tooling is insufficient, and avoid writing data with raw SQL unless the user explicitly asks or there is a clear operational need.

Before destructive database work, create a local DDEV database dump unless the user explicitly says not to.

## Cache, Environment, And Config

Use Symfony commands for cache work:

- `ddev exec php bin/console cache:clear`
- `ddev exec php bin/console cache:warmup`
- `ddev exec php bin/console debug:config <package>`
- `ddev exec php bin/console debug:container <service>`
- `ddev exec php bin/console debug:autowiring <query>`

Do not change production environment files, deployment settings, or secrets unless the user explicitly confirms the exact production action.

Prefer `.env.local` or DDEV-provided environment configuration for local/dev-only values. Do not commit real secrets.

## Frontend Assets

For Symfony frontend work, follow the project's existing Tailwind, Stimulus, and Webpack Encore setup.

- Do not add inline `<script>` blocks in Twig. Put behavior in an existing Stimulus controller or create a new lazy Stimulus controller when the behavior is non-trivial.
- Do not add inline `style=""` attributes or page-specific `<style>` blocks unless there is a clear technical reason. Prefer Tailwind utility classes and existing design tokens/config.
- Reuse existing Tailwind patterns before introducing new arbitrary values or one-off class combinations.
- Import JavaScript and CSS through the existing asset entrypoints. Do not add CDN scripts or styles in Twig unless explicitly requested.
- Before changing Webpack Encore config, inspect the existing `webpack.config.js`, `package.json`, and entrypoint structure.
- After frontend asset changes, run the project's defined build/check script through DDEV, such as `ddev exec npm run build` or the existing equivalent. Do not invent scripts.

## Messenger, Fixtures, And Jobs

Use console commands for operational Symfony tasks:

- `ddev exec php bin/console messenger:consume <transport> --limit=<n> --time-limit=<seconds>`
- `ddev exec php bin/console messenger:failed:show`
- `ddev exec php bin/console messenger:failed:retry`
- `ddev exec php bin/console doctrine:fixtures:load`

Avoid long-running consumers without limits in an assistant session. Use small limits for diagnosis and verification.

Do not load fixtures into a shared or staging database unless the user explicitly asks and the target environment is clear.

## Quality Checks

After Symfony code or config changes, run the smallest relevant verification:

- `ddev exec php bin/console lint:container`
- `ddev exec php bin/console lint:twig templates/` when Twig changed
- `ddev exec php bin/console lint:yaml config/` when YAML changed
- `ddev exec php bin/console doctrine:schema:validate` when Doctrine mappings changed
- `ddev exec php bin/phpunit` or the project test command when behavior changed
- `ddev composer validate` when Composer files changed
- The project-defined frontend build/check script when Tailwind, Stimulus, Webpack Encore, or asset entrypoints changed

Use project-defined scripts when present. Do not add broad tooling or new dependencies just to verify a small change.
