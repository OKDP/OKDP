# OKDP — Use Cases

This document lists **use cases** proposed as potential areas of work for the OKDP ecosystem. Each use case describes a user-facing scenario that OKDP assets and/or products could support.

> ⚠️ **This is not an implementation commitment.** The use cases below represent potential axes of contribution. Whether any of them is delivered depends on contributors stepping forward and on the overall capacity of the ecosystem.

## Audience

- **Platform engineers** evaluating OKDP as a source of reusable data-platform components.
- **System integrators** building data-platform deployments on top of OKDP.
- **Contributors** looking for areas where they can help.

## Use cases

| # | Title | Scope | Primary actor |
| :--- | :--- | :--- | :--- |
| [UC-01](use-case-01-consume-assets.md) | Consume individual OKDP assets (à la carte) | Middlewares | Platform engineer with an existing data platform |
| [UC-02](use-case-02-deploy-stack.md) | Deploy a working end-to-end data stack | Middlewares & Workloads | Platform engineer (greenfield) |
| [UC-03](use-case-03-gitops-middlewares.md) | Industrialize middleware deployment via GitOps | Middlewares | Platform engineer |
| [UC-04](use-case-04-workloads-frontend.md) | Run and monitor data workloads through a dedicated frontend | Workloads | Data user |
| [UC-05](use-case-05-control-plane.md) | Manage middlewares via an opinionated OKDP Control Plane | Middlewares | Platform admin |

Gaps and contribution opportunities for each use case are tracked as GitHub Issues labelled `uc-01` … `uc-05`.

## Reference

- [`assets-catalog.md`](assets-catalog.md) — Inventory of OKDP assets available for consumption (Helm charts, Docker images, plugins, examples).

## How to contribute

- **Propose a new use case** or an alternative implementation → open a GitHub Discussion.
- **Refine an existing use case** (wording, scope, gaps) → open a pull request against the corresponding file.
- **Commit to work on a listed gap** → comment on the matching GitHub Issue (`uc-XX` label).

## Open questions

- Are there **other structural use cases** missing from this list?
- Are there **alternative implementations** to propose for existing use cases?
- Are there **additional contributors** to mention for the listed work items?
