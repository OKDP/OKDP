# OKDP connection

## 1. Purpose

OKDP needs to deploy services packaged as Helm charts. These services must be connected to one another to provide a complete, unified platform.

Services must be configured to honor connections between them, therefore the concepts for connecting these services must be defined.

## 2. Terms

| Term | Meaning |
|---|---|
| claimer | Represents the requester of a connection to another service and produces a `claim` |
| claim | Represents a request |
| provisioner | Represents the provider that makes it possible to fulfill a `claim` |
| contract | Defines the properties to honor by the `claimer` and the `provisioner` |
| connection | An instance of a contract that describes how to access a resource. It can be defined by a provisioner handling a claim, by a component providing it, or manually. |
| binding | Links a `claim` to the `connection` that fulfills it |

## 3. Binding

- A `connection` can exist without a `claim`. A `claimer` uses a `connection` only through a bound `claim`.
- A `claim` names its target: a `provisioner` or an existing `connection`. A `claim` that names no target is not bound.
- The same declarations always give the same binding. OKDP never searches for a target that matches the requirements of a `claim`.
- The requirements of a `claim` are checked against the `connection`. If one is not met, the `claim` is not bound and the reason names the requirement.
