# TatanCorp Shared Workflows

Reusable CI/CD workflows for all TatanCorp services.

## Workflows

| Workflow | Purpose | Used by |
|---|---|---|
| `ci-vite.yml` | Lint + Vitest + Vite build | site, cvbuilder |
| `ci-python.yml` | Pytest suite | backend |
| `deploy-vite.yml` | Build + SSH/rsync deploy | site, cvbuilder |
| `deploy-python.yml` | SSH/rsync deploy | backend |
| `report-failure.yml` | Auto-create issue + assign Copilot on failure | all |

## Usage

```yaml
jobs:
  test:
    uses: ethanke/.github/.github/workflows/ci-vite.yml@main
    with:
      env-file: |
        MY_VAR=value
        NODE_ENV=production

  deploy:
    needs: test
    if: github.event_name == 'push'
    uses: ethanke/.github/.github/workflows/deploy-vite.yml@main
    with:
      app-name: my-app
      env-file: |
        SECRET=${{ secrets.MY_SECRET }}
        NODE_ENV=production
    secrets: inherit

  report-failure:
    needs: [test, deploy]
    if: always() && (needs.test.result == 'failure' || needs.deploy.result == 'failure')
    uses: ethanke/.github/.github/workflows/report-failure.yml@main
    with:
      failed-job: ${{ needs.test.result == 'failure' && 'test' || 'deploy' }}
    secrets: inherit
```
