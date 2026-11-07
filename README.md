# Backstage Software Templates

A collection of [Backstage](https://backstage.io/) Software Templates for scaffolding services.

## Available Templates

| Template | Description |
|---|---|
| `python-app` | Provisions a Flask application with Helm, CI/CD pipeline, ArgoCD, and TechDocs |

---

## python-app

Scaffolds a fully wired Python Flask microservice with:

- **Flask application** — `GET /api/v1/info` and `GET /api/v1/healthz`
- **Dockerfile** — `python:3.14-alpine`
- **Helm chart** — `values-dev.yaml` and `values-prd.yaml`
- **GitHub Actions CI/CD** — Docker build+push + ArgoCD sync on a self-hosted runner
- **ArgoCD support** — `charts/argocd/values-argo.yaml`
- **TechDocs** — `mkdocs.yaml` + `docs/index.md`
- **`catalog-info.yaml`** — registers the component in Backstage

### Template Parameters

| Parameter | Type | Allowed values |
|---|---|---|
| `component_id` | string (`^[a-zA-Z][a-zA-Z0-9-_]+$`) | Name of the microservice |
| `environment` | enum | `dev`, `prd` |

### What the Template Does

When triggered in Backstage, the scaffolder runs three steps:

1. **Fetch & render** — copies `python-app/template/` substituting `${{values.app_name}}` and `${{values.app_env}}`
2. **Publish** (`publish:github`) — creates a **public** GitHub repository under `github.com/ruben-pinzon/<component_id>`
3. **Register** (`catalog:register`) — registers `catalog-info.yaml` in the Backstage catalog

### Prerequisites

Your Backstage instance must have GitHub integration configured. The generated repo requires:
- `DOCKERHUB_TOKEN`, `ARGOCD_PASSWORD` (GitHub Actions secrets)
- `DOCKERHUB_USERNAME` (GitHub Actions variable)

### Generated Repository Structure

```
<component_id>/
├── .github/workflows/<component_id>-cicd.yaml
├── Dockerfile
├── catalog-info.yaml
├── charts/
│   ├── <component_id>/     # Helm chart (Deployment, Service, Ingress, HTTPRoute)
│   │   ├── values-dev.yaml
│   │   ├── values-prd.yaml
│   │   └── templates/
│   └── argocd/
│       └── values-argo.yaml
├── docs/index.md
├── k8s/                    # Static reference manifests (hardcoded name)
├── requirements.txt
└── src/
    ├── app.py
    └── mkdocs.yaml
```

> **Note:** `k8s/` contains static manifests for reference only. ArgoCD uses the Helm chart in `charts/<component_id>/`.

### Register in Backstage

```
https://github.com/rubenpinzon-sketch/backstage-software-templates/blob/main/python-app/template.yaml
```

## License

[Apache License 2.0](LICENSE)
