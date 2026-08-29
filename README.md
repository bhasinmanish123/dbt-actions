# dbt-actions

Central **reusable CI/CD workflow** for dbt projects.

## Workflow: `dbt-reusable.yml`

Runs based on the `stage` input:

| stage | What runs |
|-------|-----------|
| `ci`  | Lint (YAML + SQL) → dbt build to **DEV** |
| `cd`  | dbt build to **PROD** |

## How a dbt repo uses it

Create `.github/workflows/dbt_cicd.yml` in your dbt project:

```yaml
name: dbt CI/CD
on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  ci:
    if: github.event_name == 'pull_request'
    uses: bhasinmanish123/dbt-actions/.github/workflows/dbt-reusable.yml@main
    with:
      stage: ci
    secrets:
      SNOWFLAKE_ACCOUNT: ${{ secrets.SNOWFLAKE_ACCOUNT }}
      SNOWFLAKE_USER: ${{ secrets.SNOWFLAKE_USER }}
      SNOWFLAKE_PASSWORD: ${{ secrets.SNOWFLAKE_PASSWORD }}

  cd:
    if: github.event_name == 'push'
    uses: bhasinmanish123/dbt-actions/.github/workflows/dbt-reusable.yml@main
    with:
      stage: cd
    secrets:
      SNOWFLAKE_ACCOUNT: ${{ secrets.SNOWFLAKE_ACCOUNT }}
      SNOWFLAKE_USER: ${{ secrets.SNOWFLAKE_USER }}
      SNOWFLAKE_PASSWORD: ${{ secrets.SNOWFLAKE_PASSWORD }}
```

## Credentials

Secrets live in the **calling repo** (or org-level), NOT here.
GitHub passes them through to this reusable workflow at runtime.
This is a GitHub security rule — reusable workflows receive secrets, they cannot store them.
