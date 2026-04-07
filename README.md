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

## Deployments

Deployments are handled by **ArgoCD Image Updater**, which watches GHCR directly and updates the Kustomize overlay in `stellarlive-infra` automatically when a new image is pushed. No workflow is needed for this step.

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
