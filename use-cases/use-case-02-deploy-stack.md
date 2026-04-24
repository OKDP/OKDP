# UC-02 — Deploy a working end-to-end data stack

**Status:** Proposal — open for contribution<br>
**Scope:** Middlewares & Workloads<br>
**Actor:** Platform engineer bootstrapping a new data platform from scratch.

## Goal

Deploy a working, end-to-end OKDP data stack — middlewares plus representative example workloads — on a fresh Kubernetes cluster, using **pure `helm install`** and standard Kubernetes tooling. No OKDP abstraction layer, no OKDP backend, no OKDP UI.

## OKDP value

Provides a reference, reproducible blueprint showing how OKDP assets fit together end-to-end. It addresses:

- Platform engineers who already run their own ops tooling or internal portal and want a blueprint to adapt.
- Teams deploying OKDP in pure GitOps who want a transparent, opinionated reference without adopting the OKDP Control Plane.

This is the "sandbox" layer of the ecosystem — the proof that the unit components from [UC-01](use-case-01-consume-assets.md) assemble into a functional whole.
