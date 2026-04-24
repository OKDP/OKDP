# UC-04 — Run and monitor data workloads through a dedicated frontend

**Status:** Proposal — open for contribution<br>
**Scope:** Workloads<br>
**Actor:** Data user (data engineer, data scientist, analyst) running and monitoring workloads without Kubernetes exposure.

## Goal

Offer a dedicated OKDP frontend focused purely on the workload layer: submit a Spark application, run SQL queries, list running applications, and access provisioned environments. No additional abstraction on top — the frontend exposes each component's native application abstraction (e.g., the `SparkApplication` CRD for Spark).

## OKDP value

Gives data users a **domain-level interface** for their workloads — submitting Spark jobs, SQL queries, monitoring running applications — without any Kubernetes exposure. Complements GitOps and platform tooling, which stay focused on middlewares.

## Market equivalent

Databricks Workspace Console.

