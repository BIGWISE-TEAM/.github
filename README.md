# BIGWISE-TEAM/.github

Org-level GitHub configuration for BIGWISE-TEAM.

## Reusable Workflows

Located at `.github/workflows/` — callable from any repository in the org via `workflow_call`.

### `build-dotnet.yml`

Builds and pushes a .NET service Docker image to GHCR.

```yaml
jobs:
  build:
    uses: BIGWISE-TEAM/.github/.github/workflows/build-dotnet.yml@main
    with:
      dotnet-version: '7.0'
      service-name: slms-sales
      project-path: SLMSSales/SLMSSales.csproj
    secrets: inherit
```

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `dotnet-version` | yes | — | .NET SDK version (e.g. `6.0`, `7.0`) |
| `service-name` | yes | — | GHCR image name (e.g. `slms-sales`) |
| `project-path` | yes | — | Relative path to `.csproj` |
| `docker-context` | no | `.` | Docker build context directory |
| `dockerfile-path` | no | `Dockerfile` | Path to Dockerfile |

---

### `build-node.yml`

Builds and pushes a Node.js service Docker image to GHCR.

```yaml
jobs:
  build:
    uses: BIGWISE-TEAM/.github/.github/workflows/build-node.yml@main
    with:
      service-name: stellarlive-notification
    secrets: inherit
```

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `node-version` | no | `20` | Node.js version |
| `service-name` | yes | — | GHCR image name |
| `docker-context` | no | `.` | Docker build context directory |
| `dockerfile-path` | no | `Dockerfile` | Path to Dockerfile |

---

### `build-java.yml`

Builds and pushes a Java service Docker image to GHCR.

```yaml
jobs:
  build:
    uses: BIGWISE-TEAM/.github/.github/workflows/build-java.yml@main
    with:
      service-name: my-java-service
    secrets: inherit
```

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `java-version` | no | `21` | Java version |
| `service-name` | yes | — | GHCR image name |
| `docker-context` | no | `.` | Docker build context directory |
| `dockerfile-path` | no | `Dockerfile` | Path to Dockerfile |

---

### `deploy-update-tag.yml`

Updates the Kustomize image tag in `stellarlive-infra` to trigger a GitOps deployment via ArgoCD.

```yaml
jobs:
  deploy:
    uses: BIGWISE-TEAM/.github/.github/workflows/deploy-update-tag.yml@main
    with:
      service-name: slms-sales
      image-tag: ${{ needs.build.outputs.sha }}
      environment: production
    secrets:
      INFRA_PAT: ${{ secrets.INFRA_PAT }}
```

| Input | Required | Description |
|-------|----------|-------------|
| `service-name` | yes | Matches `apps/<service-name>/` in stellarlive-infra |
| `image-tag` | yes | Tag to write into the Kustomize overlay |
| `environment` | yes | `testing` or `production` |

| Secret | Required | Description |
|--------|----------|-------------|
| `INFRA_PAT` | yes | PAT with `repo` scope on `stellarlive-infra` |

---

## Typical `ci.yml` in a service repo

```yaml
name: CI

on:
  push:
    branches: [main, develop]

jobs:
  build:
    uses: BIGWISE-TEAM/.github/.github/workflows/build-dotnet.yml@main
    with:
      dotnet-version: '7.0'
      service-name: slms-sales
      project-path: SLMSSales/SLMSSales.csproj
    secrets: inherit

  deploy:
    needs: build
    if: github.ref == 'refs/heads/main'
    uses: BIGWISE-TEAM/.github/.github/workflows/deploy-update-tag.yml@main
    with:
      service-name: slms-sales
      image-tag: ${{ github.sha }}
      environment: production
    secrets:
      INFRA_PAT: ${{ secrets.INFRA_PAT }}
```

## Directory Structure

```
.github/
├── workflow-templates/   # Future: starter workflow templates for new repos
└── workflows/
    ├── build-dotnet.yml
    ├── build-node.yml
    ├── build-java.yml
    └── deploy-update-tag.yml
```
