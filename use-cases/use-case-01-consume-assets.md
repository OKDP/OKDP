# UC-01 — Consume individual OKDP assets (à la carte)

**Status:** Proposal — open for contribution<br>
**Scope:** Middlewares<br>
**Actor:** Platform engineer with an existing data platform.

## Goal

Pick and reuse specific OKDP assets — Docker images, Helm charts, authentication plugins, extensions, usage examples — and integrate them into an existing data platform, **without adopting** the OKDP frontend, Control Plane, or opinionated deployment model.

## OKDP value

OKDP assets are production-tested and security-hardened: authentication (OIDC, JWT, STS), observability, and cross-component wiring are already in place. Instead of each organization re-hardening raw upstream components (Spark, Trino, Polaris…), consumers pick up OKDP's consolidated assets and drop them into their stack. Improvements made downstream can be contributed back upstream, benefiting the rest of the ecosystem.

This is the lightest adoption mode of the OKDP ecosystem — no full-stack commitment required.

## Available OKDP assets

See [assets-catalog.md](assets-catalog.md).
