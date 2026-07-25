# microk8s-gitops-pipeline

A GitOps-driven deployment architecture for running Cisco Network Services Orchestrator (NSO) on a local MicroK8s cluster.

This project bridges Network Automation and DevOps practices by replacing manual container deployments with a declarative, automated continuous deployment workflow utilizing Argo CD and Kustomize.

---

## 🏗️ Architecture & Component Overview

The project leverages a pull-based GitOps model. All infrastructure and application states are declared directly within this repository, and Argo CD continuously ensures the local MicroK8s cluster matches the desired state.

### 🌐 Traffic Routing & Application Stack
Incoming traffic follows a proxy-chained flow to handle API routing, certificate termination, and application traffic management securely:

**User Request** → **MicroK8s Ingress (`dev.nso`)** → **NGINX Reverse Proxy** → **Cisco NSO Container**

### ⚙️ Deployment & Sync Flow
* **State Declaration:** Manifests, deployment specs, and configuration overrides are version-controlled, using Kustomize to structure overlays cleanly.
* **Continuous Deployment (CD):** Argo CD actively monitors the deployment directories. When changes are detected (such as an updated container image tag, environment variable update, or modified configuration), Argo CD automatically synchronizes and applies the updated state to the local MicroK8s cluster.

---

## 🗂️ Project Structure

The repository is organized to support Kustomize, allowing Argo CD to natively compile and deploy the manifest bundle.

```text
.
├── argocd-nso-app.yaml            # Argo CD Application definition
└── deployments/
    └── base/
        ├── kustomization.yaml     # Resource compiler
        ├── nginx-config.yaml      # ConfigMap for NGINX routing rules
        ├── nginx-proxy.yaml       # NGINX Deployment and Service
        ├── nso-deployment.yaml    # Cisco NSO Stateful workload definition
        ├── nso-ingress.yaml       # Host-based routing (dev.nso)
        └── nso-storage.yaml       # Persistent Volume mapping for NSO CDB
