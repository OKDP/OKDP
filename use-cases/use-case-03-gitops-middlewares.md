# UC-03 — Industrialize middleware deployment via GitOps

**Status:** Proposal — open for contribution<br>
**Scope:** Middlewares<br>
**Actor:** Platform engineer deploying OKDP middlewares via a GitOps orchestrator.

## Goal

Industrialize the deployment of OKDP middlewares through a GitOps orchestrator — FluxCD (with Weave GitOps UI), ArgoCD (with ArgoCD UI), KuboCD, or any other — backed by a reference architecture of pre-wired repositories and manifests.

## OKDP value

Deliver ready-to-adapt **reference deployments** — one per GitOps orchestrator — containing the full set of repositories and manifests needed to instantiate OKDP middlewares in an industrialized, reproducible way. Each reference serves as a blueprint for platform teams rolling out their own GitOps pipeline.

Monitoring and visibility are delegated to the orchestrator's native UI (Weave GitOps, ArgoCD UI) — OKDP does not introduce its own at this level.
