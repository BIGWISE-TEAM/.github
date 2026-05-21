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

## Deployments (GitOps)

After a successful build on `push` events, the workflow automatically:
1. Tags the image with the short commit SHA (e.g., `abc1234`)
2. Clones `stellarlive-infra` and updates the kustomize overlay's `newTag`
3. Pushes the change — ArgoCD detects it and syncs (auto-rollout)

**Requirements:**
- Org secrets `ARGOCD_APP_ID` and `ARGOCD_APP_PRIVATE_KEY` — from the `stellarlive-argocd` GitHub App (installed on `stellarlive-infra` with contents:write)
- The service must have an overlay at `apps/{service-name}/overlays/{environment}/kustomization.yaml`

**Opt out:** Pass `deploy: false` in the workflow call to skip auto-deploy (e.g., for libraries like liveentity).

**Deploy to production:** Pass `deploy-environment: 'production'` (default is `testing`).

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
```

## Directory Structure

```
.github/
├── workflow-templates/   # Future: starter workflow templates for new repos
└── workflows/
    ├── build-dotnet.yml
    ├── build-node.yml
    └── build-java.yml
```
