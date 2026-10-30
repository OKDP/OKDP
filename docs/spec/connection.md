# OKDP connection

## 1. Purpose

OKDP needs to deploy services packaged as Helm charts. These services must be connected to one another to provide a complete, unified platform.

Services must be configured to honor connections between them, therefore the concepts for connecting these services must be defined.

## 2. Terms

| Term | Meaning |
|---|---|
| claimer | Represents the requester of a connection to another service and produces a `claim`
| claim | Represents a request
| provisioner | Represents the provider that makes it possible to fulfill a `claim`
| contract | Defines the properties to honor by the `claimer` and the `provisioner`
| connection | An instance of a contract where the values for the `claim` and the `provisioner` are generated
