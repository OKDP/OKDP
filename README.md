<div align="center">

<img src="https://raw.githubusercontent.com/OKDP/OKDP/main/logo/inverted/okdp-inverted.png" alt="OKDP Logo" width="320" />

# OKDP: Open Kubernetes Data Platform

A cloud-native, open-source data platform for Kubernetes.  
Modular, sovereign, and community-driven.

[Get Started](#getting-started) · [Architecture](#architecture) · [Community](#community) · [Roadmap](https://okdp.io/roadmap/)

[![License Apache2](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](./LICENSE)
[![okdp.io](https://img.shields.io/badge/website-okdp.io-blue)](https://okdp.io)

</div>

---

## 🏗️ Architecture

OKDP is structured around two complementary layers:

- **Data Platform**: A curated catalog of battle-tested open-source data tools. Each component is independently deployable, with no lock-in to the Control Plane.
- **Control Plane**: A unified management layer ([OKDP Server](https://github.com/OKDP/okdp-server) + [OKDP UI](https://github.com/OKDP/okdp-ui)) for deploying, configuring, and monitoring platform components across one or multiple clusters.

---

## 📦 Data Platform Components

| Component       | Description                                                                                                                                       | Helm Chart                                                                                                                         | OKDP Repo                                                                                                                                                                                                                                                      |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Apache Spark    | Distributed data processing engine with Kubernetes-native operator, OIDC-enabled History Server, real-time Web Proxy, and pre-built Docker images | [spark-operator](https://kubeflow.github.io/spark-operator) / [spark-history-server](https://github.com/OKDP/spark-history-server) | [spark-images](https://github.com/OKDP/spark-images) · [spark-history-server](https://github.com/OKDP/spark-history-server) · [spark-auth-filter](https://github.com/OKDP/okdp-spark-auth-filter) · [spark-web-proxy](https://github.com/OKDP/spark-web-proxy) |
| Hive Metastore  | Centralized metadata repository for data lakes, storing table schemas, partitions, and data locations for Spark, Trino, and other engines         | [hive-metastore](https://github.com/OKDP/hive-metastore)                                                                           | [hive-metastore](https://github.com/OKDP/hive-metastore)                                                                                                                                                                                                       |
| Trino           | Distributed SQL query engine for fast, interactive analytics across S3, Hive Metastore, and other data sources                                    | [trinodb/trino](https://trinodb.github.io/charts)                                                                                  | upstream                                                                                                                                                                                                                                                       |
| Apache Superset | Enterprise BI platform for interactive dashboards, SQL Lab, and data visualization with OIDC authentication                                       | [okdp-superset](https://github.com/OKDP/okdp-superset)                                                                             | [okdp-superset](https://github.com/OKDP/okdp-superset)                                                                                                                                                                                                         |
| Apache Airflow  | Workflow orchestration platform for authoring, scheduling, and monitoring data pipelines and Spark jobs                                           | [apache/airflow](https://airflow.apache.org)                                                                                       | upstream                                                                                                                                                                                                                                                       |
| JupyterHub      | Multi-user interactive notebook environment with PySpark integration, S3 file browsing, and OIDC authentication                                   | [jupyterhub](https://hub.jupyter.org/helm-chart/)                                                                                  | [jupyterlab-docker](https://github.com/OKDP/jupyterlab-docker)                                                                                                                                                                                                 |
| OKDP Examples   | Hands-on examples, Jupyter notebooks, and data workflows showcasing the OKDP platform end-to-end                                                  | [okdp-examples](https://github.com/OKDP/okdp-examples)                                                                             | [okdp-examples](https://github.com/OKDP/okdp-examples)                                                                                                                                                                                                         |

## ⚙️ Infrastructure Prerequisites

| Component     | Description                                                            | Helm Chart                                                         |
| ------------- | ---------------------------------------------------------------------- | ------------------------------------------------------------------ |
| cert-manager  | Kubernetes-native TLS certificate management (CNCF Graduated)          | [jetstack/cert-manager](https://charts.jetstack.io)                |
| ingress-nginx | NGINX Ingress Controller for HTTP/HTTPS routing                        | [ingress-nginx](https://kubernetes.github.io/ingress-nginx)        |
| CloudNativePG | PostgreSQL operator for automated cluster management (CNCF Sandbox)    | [cnpg/cloudnative-pg](https://cloudnative-pg.github.io/charts)     |
| Keycloak      | Identity and access management with OIDC support for all OKDP services | [codecentric/keycloakx](https://codecentric.github.io/helm-charts) |
| SeaweedFS     | S3-compatible distributed object storage for data lake and event logs  | [seaweedfs/helm](https://seaweedfs.github.io/seaweedfs/helm)       |

> To deploy all infrastructure prerequisites at once, use the [okdp-prerequisites umbrella chart](https://github.com/OKDP/helm-handbook/tree/main/modules/prerequisites/okdp-prerequisites).

## 🎛️ Control Plane

The OKDP Control Plane provides a unified interface to deploy, configure, and monitor all platform components across one or multiple Kubernetes clusters.

| Component   | Description                                                                                                      | Repo                                               |
| ----------- | ---------------------------------------------------------------------------------------------------------------- | -------------------------------------------------- |
| OKDP Server | REST API backend for managing deployments, clusters, projects, GitOps repositories, and package catalogs         | [okdp-server](https://github.com/OKDP/okdp-server) |
| OKDP UI     | Web interface for deploying and monitoring all OKDP services, with GitOps and direct Kubernetes deployment modes | [okdp-ui](https://github.com/OKDP/okdp-ui)         |

> The Control Plane is optional. All Data Platform components can be deployed independently via Helm without it.

---

## 🚀 Getting Started

### Option 1: Sandbox (recommended)

The fastest way to explore OKDP. Spins up a full, pre-configured platform on [Kind](https://kind.sigs.k8s.io/) with a single command.

→ [okdp-sandbox](https://github.com/OKDP/okdp-sandbox)

### Option 2: Manual install

Deploy components individually on your own cluster using the Helm-based install guides.

See the [helm-handbook](https://github.com/OKDP/helm-handbook) for install guides.

---

## 🤝 Community & TOSIT

OKDP is supported by [TOSIT](https://tosit.fr) (The Open Source I Trust), initiated by DGFiP, Orange, and other organizations. The goal is a sovereign, powerful, and fully open-source data stack accessible to everyone.

- 🌐 **Website**: [okdp.io](https://okdp.io)
- 💬 **Discussions**: [Mattermost OKDP (TOSIT)](https://framateam.org/tosit/channels/okdp)
- 📅 **Weekly technical meeting**: Every Wednesday at 10:00 CET
- 🐳 **Docker images**: [Quay.io/okdp](https://quay.io/organization/okdp)
- ☕ **Java artifacts**: [Maven Central (io.okdp)](https://central.sonatype.com/namespace/io.okdp)
- 🤝 **Contribute**: [okdp.io#community](https://okdp.io#community)

## 🗺️ Roadmap

See the [official roadmap](https://okdp.io/roadmap/) on okdp.io (v1.0.0 planned for June 2026).

## 🤲 Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for documentation standards and review process.

## 🏛️ Governance

See [GOVERNANCE.md](GOVERNANCE.md) for project governance, roles, and decision-making.

## 📄 License

[Apache License 2.0](LICENSE)