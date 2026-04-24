# UC-05 — Manage middlewares via an opinionated OKDP Control Plane

**Status:** Proposal — open for contribution<br>
**Scope:** Middlewares<br>
**Actor:** Platform admin managing OKDP middlewares at a high level of abstraction.

## Goal

Deploy, configure, and operate OKDP middlewares through an opinionated **Control Plane** (`okdp-server`, `okdp-ui`, `okdp-cli`). Middleware provisioning and usage become self-service — via the dedicated OKDP frontend or via `kubectl` on top of a simplified abstraction — instead of requiring direct low-level Helm or GitOps handling.

## OKDP value

The central software component of the OKDP platform. The Control Plane:

- Orchestrates middleware deployment and hides GitOps complexity.
- Provides **dynamic cross-component wiring** — automatically connecting components (e.g., Trino ↔ Polaris Catalog, Spark ↔ Hive Metastore) instead of relying on static per-component configuration.
- Centralizes authentication, secret management, multi-tenancy, and multi-cluster deployment.

This is the piece that turns OKDP from a collection of upstream assets into a coherent platform product.

## Market equivalents

Databricks Control Plane, Apache Ambari, Cloudera Management Console.
