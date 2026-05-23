# Forge Platform — Open Source DevOps Automation (AWX Alternative)

**Self-hosted, Apache 2.0-licensed automation platform** for Ansible, infrastructure-as-code, drift detection, and policy-as-code. A modernized fork of [Ansible AWX](https://github.com/ansible/awx) with a Kubernetes-native control plane, native OIDC + WebAuthn, OpenTelemetry observability, and a declarative operator for GitOps workflows.

[**📚 Documentation**](https://forgeplatform.github.io) · [**🚀 Quick Start**](https://forgeplatform.github.io/docs/deployment.html) · [**☸️ Kubernetes**](https://forgeplatform.github.io/docs/kubernetes.html) · [**🤖 Operator**](https://forgeplatform.github.io/docs/operator-v1.html) · [**📦 Releases**](https://forgeplatform.github.io/docs/release-2026.05.0.html)

---

## What is Forge?

Forge Platform is a **modern, open source alternative to Ansible Tower / Ansible Automation Platform (AAP) / AWX**. It runs your Ansible playbooks at scale with role-based access control (RBAC), surveys, schedules, audit trails, and a distributed task engine — all on infrastructure you own.

If you have outgrown standalone `ansible-playbook` runs but do not want to lock yourself into a vendor SaaS or a Red Hat subscription, Forge is for you.

### Why a fork of AWX?

AWX is a great foundation but has accumulated legacy patterns over years of stewardship. Forge keeps the proven task engine, RBAC model, and inventory abstractions, while modernizing everything around them:

| Area | Legacy AWX | Forge Platform |
|------|------------|----------------|
| Runtime | Python 3.9, Django 3 | **Python 3.12, Django 4.2** |
| Frontend | Patternfly (jQuery roots) | **React 18 + TypeScript + Vite** |
| Auth | LDAP + SAML + Social | **+ Native OIDC, WebAuthn, hardware keys** |
| Deployment | k8s operator or AWX-operator only | **Docker Compose, Helm chart, native Kubernetes operator with 9 CRDs** |
| Observability | Prometheus metrics | **OpenTelemetry traces + metrics + logs** |
| Policy | None built-in | **OPA (Open Policy Agent) integration** |
| IaC scanning | None | **Native Terraform/CloudFormation scanning** |
| Multi-tenancy | Organizations only | **Hard tenant isolation, per-tenant resource quotas** |
| AI | None | **Embedded AI assistant (Ollama + ChromaDB, self-hosted)** — *⚠️ preview, in active development* |

---

## Features

### 🎭 Job execution at scale
- **Distributed task engine** with [Receptor](https://github.com/ansible/receptor) mesh networking — execution nodes can sit behind NAT, run on-prem, or in air-gapped environments
- **Dynamic surveys** with per-job-template input forms, regex validation, conditional fields
- **Schedules**, **workflows** (DAG of jobs with conditional edges), **notifications**, **callbacks**

### 🔐 Identity, access & audit
- **RBAC** with custom roles, team membership, organization scoping
- **SSO**: LDAP, SAML 2.0, OIDC (Keycloak, Authentik, Auth0, Okta, Azure AD), social auth
- **WebAuthn / passkeys** for hardware-key second factor
- **Audit trail** — every state change recorded with actor, resource, before/after diff
- **Self-service portal** — end users request access via approval workflows

### ☸️ Kubernetes-native
- **Helm chart** — `helm install forge oci://ghcr.io/forgeplatform/forge-helm`
- **Operator v1.0.0** with 9 CRDs (`Organization`, `Team`, `Project`, `Inventory`, `Credential`, `JobTemplate`, `Schedule`, `Workflow`, `ForgeInstance`) for GitOps-style management
- **Multi-cluster** — single operator reconciles multiple Forge backends via `ForgeInstance` CR
- **OLM bundle** — install via OperatorHub or `kubectl operator install`

### 🔭 Observability built in
- **OpenTelemetry** traces from API → task dispatcher → execution worker, all correlated by trace ID
- **Prometheus metrics** for job throughput, queue depth, worker utilization
- **Structured logs** to stdout, OpenTelemetry log pipeline, or Loki
- **Drift detection** — periodic compare of declared inventory vs. actual cloud/host state, alerts on divergence

### 🛡️ Policy & compliance
- **OPA (Open Policy Agent)** sidecar — enforce policies on job runs (no execution against prod after-hours, no `--check=no`, mandatory tags, etc.)
- **IaC scanning** — Terraform, CloudFormation, Pulumi static analysis with [Checkov](https://github.com/bridgecrewio/checkov) integration
- **Event-driven automation** — webhook receivers trigger jobs from external systems

### 🤖 AI Assistant (optional, preview)

> ⚠️ **The AI Assistant is under active development and is not yet production-ready.** It is shipped as a preview for early feedback. APIs, models, and capabilities may change between releases. Do **not** depend on it for critical workflows yet.

- **Fully self-hosted** preview — runs Ollama + ChromaDB embedded in a single container
- Default model: `gemma3:1b` (small, fast, 2 GB pod). Swap to any Ollama-compatible model
- No data leaves your cluster — designed for privacy-first operation
- Track progress and known limitations in [forge-assistant](https://github.com/forgeplatform/forge-assistant)

---

## Quick Start

### Docker Compose (one machine, ~5 min)

```bash
git clone https://github.com/forgeplatform/forge-devops.git
cd forge-devops
cp .env.example .env
docker compose up -d
# UI on https://localhost (admin / changeme — change it!)
```

### Kubernetes (Helm)

```bash
kubectl create namespace forge
helm install forge oci://ghcr.io/forgeplatform/forge-helm --version 1.0.0 -n forge
# Or clone https://github.com/forgeplatform/forge-helm and `helm install forge ./`
```

Images are on the public **[GitHub Container Registry](https://github.com/orgs/forgeplatform/packages)** — no pull secret required.

### Kubernetes Operator (declarative / GitOps)

```bash
helm install forge-operator oci://ghcr.io/forgeplatform/forge-operator-helm \
    -n forge-operator --create-namespace \
    --set forge.token=<your-forge-PAT>
# Then commit Organization/Project/JobTemplate CRs to git
```

See [forgeplatform.github.io/docs/operator-v1.html](https://forgeplatform.github.io/docs/operator-v1.html) for full operator usage.

---

## Repositories

| Repo | Description | Language |
|------|-------------|----------|
| [**forge-devops**](https://github.com/forgeplatform/forge-devops) | Docker Compose stack, install scripts, deployment docs | Shell, YAML |
| [**forge-backend**](https://github.com/forgeplatform/forge-backend) | Django REST API + Celery task engine + Receptor mesh | Python |
| [**forge-frontend**](https://github.com/forgeplatform/forge-frontend) | React 18 + TypeScript + Vite UI | TypeScript |
| [**forge-helm**](https://github.com/forgeplatform/forge-helm) | Production Helm chart for Kubernetes | Helm |
| [**forge-operator**](https://github.com/forgeplatform/forge-operator) | Kubernetes operator (controller-runtime, 9 CRDs, OLM bundle) | Go |
| [**forge-dev-cluster**](https://github.com/forgeplatform/forge-dev-cluster) | Vagrant + k3s dev cluster (3m+4w nodes) for integration testing | Shell |
| [**forge-assistant**](https://github.com/forgeplatform/forge-assistant) | FastAPI AI assistant (Ollama + ChromaDB, self-hosted) | Python |
| [**forgeplatform.github.io**](https://github.com/forgeplatform/forgeplatform.github.io) | Documentation site source | HTML |

---

## Documentation

Full docs are at **[forgeplatform.github.io](https://forgeplatform.github.io)**:

- [Architecture Overview](https://forgeplatform.github.io/docs/architecture.html)
- [Docker Deployment Guide](https://forgeplatform.github.io/docs/deployment.html)
- [Kubernetes Deployment](https://forgeplatform.github.io/docs/kubernetes.html)
- [Operator v1.0.0 (Multi-cluster, Workflow DAG, OLM)](https://forgeplatform.github.io/docs/operator-v1.html)
- [User Handbook](https://forgeplatform.github.io/docs/user-handbook.html)
- [Administrator Handbook](https://forgeplatform.github.io/docs/admin-handbook.html)
- [API Reference](https://forgeplatform.github.io/docs/api-reference.html)
- [Release Notes — v2026.05.0](https://forgeplatform.github.io/docs/release-2026.05.0.html)

---

## FAQ

### Is Forge Platform really free?

Yes. Apache License 2.0. No paid tier, no enterprise lock-in, no telemetry phone-home. The container images on [ghcr.io/forgeplatform](https://github.com/orgs/forgeplatform/packages) are public and unlimited.

### How is Forge different from AWX?

Forge keeps the proven AWX task engine and RBAC model, but rewrites everything around them on a modern stack (Python 3.12, Django 4.2, React 18, TypeScript), adds first-class Kubernetes support via a native operator with 9 CRDs, integrates OpenTelemetry for observability, ships an OPA sidecar for policy enforcement, and bundles an optional self-hosted AI assistant. See the [comparison table](#why-a-fork-of-awx) above.

### How is Forge different from Ansible Automation Platform (AAP / Tower)?

AAP is Red Hat's commercial offering with paid support, certified content collections, and proprietary features (e.g., automation analytics SaaS). Forge has no commercial tier — you self-host everything. Forge is closer to the AWX experience but more modern; if you need Red Hat support contracts, AAP is your choice. If you want full sovereignty and a modern codebase, Forge is.

### Can I migrate from AWX to Forge?

Forge's data model is largely compatible with AWX (since it's a fork). A migration tool is on the [Phase B roadmap](https://github.com/forgeplatform/forge-devops/blob/main/docs/RELEASE_NOTES_v2026.05.0.md). In the meantime, the operator's GitOps approach lets you re-declare your AWX resources as CRs and reconcile them into Forge.

### What's the minimum hardware to run Forge?

- **Single-machine (Docker Compose)**: 4 GB RAM, 2 vCPU, 20 GB disk
- **Production (Kubernetes)**: 3 worker nodes, 8 GB RAM each, plus Postgres + Redis backing services
- **Reference dev cluster**: [forge-dev-cluster](https://github.com/forgeplatform/forge-dev-cluster) is a 3-server + 4-worker k3s Vagrant setup, ~28 GB RAM total

### Is the AI assistant ready to use?

**Not yet — it is under active development as of the latest release.** The `forge-assistant` is shipped as a preview to gather early feedback; APIs and capabilities may change. Do not use it for production-critical workflows.

### Does the AI assistant send my data anywhere?

No. The optional `forge-assistant` (preview) runs Ollama and ChromaDB embedded in a single container. Models run locally on CPU or GPU. No outbound calls to OpenAI, Anthropic, or any third party — privacy-first by design.

### What about security disclosure?

Each repository has a [SECURITY.md](https://github.com/forgeplatform/forge-backend/blob/main/SECURITY.md). Email security issues privately to **office@krletron.xyz**. Public CVE process for confirmed vulnerabilities.

### Who maintains Forge?

Founder and lead maintainer: **Krstan Vjestica**. Contributions welcome — see [CONTRIBUTING.md](https://github.com/forgeplatform/forge-devops/blob/main/docs/10-contributing-guide.md).

---

## Contributing

We welcome contributions. Read the [centralized contributing guide](https://github.com/forgeplatform/forge-devops/blob/main/docs/10-contributing-guide.md) for git workflow, commit conventions, and PR process.

For bug reports: open an issue on the relevant repository. For security issues: email **office@krletron.xyz** (see [SECURITY.md](https://github.com/forgeplatform/forge-backend/blob/main/SECURITY.md)).

---

## Heritage & License

Forge is a modernized fork of [Ansible AWX](https://github.com/ansible/awx) (Apache License 2.0), originally developed by Red Hat and the Ansible community. Full attribution is preserved in [forge-backend/NOTICE](https://github.com/forgeplatform/forge-backend/blob/main/NOTICE).

All Forge Platform repositories are licensed under the [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0).

---

**Search keywords**: open source Ansible automation, AWX alternative, AWX fork, Ansible Tower alternative, Ansible Automation Platform open source, self-hosted CI/CD for Ansible, Kubernetes Ansible operator, GitOps Ansible, DevOps automation platform, infrastructure automation, RBAC Ansible, OpenTelemetry Ansible, OPA Open Policy Agent Ansible, self-hosted AI assistant DevOps, Receptor mesh, declarative Ansible Kubernetes
