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

## 3. Rules

### 3.1 Claim

- A `claim` holds:
  - the `contract` it follows and its version;
  - its target;
  - its requirements, checked against the `connection`;
  - the values a `provisioner` needs to create the resource. They are not checked, and are not used when the target is an existing `connection`.
- A `claim` is either bound or not bound. A `claim` that is not bound has a reason, for example a requirement that is not met, a refusal from the `provisioner`, or a `provisioner` that has not finished yet.
- When a `claim` is removed, the `connection` defined for it is removed. A `connection` that exists without a `claim` stays. What happens to the resource is defined by the `contract`.

### 3.2 Provisioner

- A `provisioner` handles the `claims` that name it. It creates the resource, defines a `connection` for the `claim`, and binds the `claim` to it.
- A `provisioner` can serve only some `claimers`, for example those of one project. It binds no other `claim`.
- A `provisioner` declares the `contracts` it handles and the `claimers` it serves, so that they can choose it.
- A `provisioner` refuses a `claim` for a version of the `contract` it does not handle, or that holds a requirement it does not know. It never ignores a requirement.
- The credentials a `provisioner` uses to create resources are never part of a `connection` it defines.

### 3.3 Binding

- A `connection` can exist without a `claim`. A service can use a `connection` directly, or through a bound `claim`.
- A `claim` names its target: a `provisioner` or an existing `connection`. A `claim` that names no target is not bound.
- The same declarations always give the same binding. OKDP never searches for a target that matches the requirements of a `claim`.
- The requirements of a `claim` are checked against the `connection`. If one is not met, the `claim` is not bound and the reason names the requirement.
- A `claim` is bound only to a `connection` of the same `contract` version.

## 4. Contracts

- Each `contract` has its own document in the `connection/` folder next to this one, for example for a database or a bucket.
- A `contract` has a version: `1`, `2`, and so on. A change that breaks existing `claimers` or `provisioners` gives a new version. A `claim` and a `connection` carry the version of their `contract`.
- A `contract` defines:
  - the properties of the `connection`;
  - the requirements a `claim` can hold, and how each one is checked against the `connection`;
  - the values a `claim` can pass to the `provisioner`;
  - what a `provisioner` guarantees;
  - what happens to the resource when its `claim` is removed.
