# Database contract

This document defines version `1` of the `database` contract. It follows the rules of [OKDP connection](../connection.md).

## 1. Purpose

Services store their state in a database. This contract defines what a `claim` for a database holds, what the `connection` gives, and what a `provisioner` guarantees.

A database here is a named database hosted by a server and reached with a username and a password. The contract applies to any engine that matches this definition, SQL or not, for example PostgreSQL, MariaDB or CouchDB. Caches and key-value stores are out of scope.

## 2. Connection

| Property | Meaning |
|---|---|
| `engine` | Name of the database engine, in lowercase, for example `postgresql` |
| `host`, `port` | Address of the server |
| `dbName` | Name of the database |
| `secretRef` | Secret holding `username`, `password` and `uri` |
| `serverVersion` | Optional. Version of the server, as numbered by the engine, for example `18` or `3.5.0` |
| `extensions` | Optional. Extensions created in the database |
| `tls` | Optional. `required`: the client must use TLS. `verify`: the client must also verify the certificate and the name of the server |

- `uri` is the connection URI documented by the engine, or its URL for an engine reached over HTTP. `username` and `password` are percent-encoded.
- When `tls` is set, `uri` carries it in the syntax of the engine.
- `extensions` applies only to an engine that has extensions.

Two examples, one SQL and one NoSQL:

| Engine | `uri` | `tls` in `uri` | `extensions` |
|---|---|---|---|
| `postgresql` | `postgresql://<username>:<password>@<host>:<port>/<dbName>` | `?sslmode=require` or `?sslmode=verify-full` | yes |
| `couchdb` | `http://<username>:<password>@<host>:<port>/<dbName>` | `https` instead of `http` | no |

## 3. Claim

| Requirement | Meaning | Met when |
|---|---|---|
| `engines` | Engines the `claimer` supports | `engine` is in the list |
| `serverVersion` | Constraint on the version of the server, for example `>=15` | `serverVersion` meets the constraint |
| `extensions` | Extensions the `claimer` needs | Each one is in `extensions` |
| `dbName` | Name of the database | `dbName` is the same |

- Every requirement is optional.
- A constraint is a list of comparisons separated by commas, for example `>=15, <18`. The operators are `=`, `!=`, `>`, `>=`, `<` and `<=`. Versions are compared number by number, and a missing number counts as `0`.
- A requirement on a property the `connection` leaves empty is not met.
- A `claim` passes no other value to the `provisioner`.

## 4. Provisioner

- Before creating anything, the `provisioner` checks the requirements against its server. If one is not met, it creates nothing.
- Each `claim` gets its own user. The user has every right on its database and none on the rest of the server: it cannot create databases or users, bypass access control, or reach another database.
- If the `claim` names no database, the `provisioner` chooses a name that is unique on the server and always the same for the same `claim`.
- Two `claims` that name the same database share it. Each one still gets its own user.
- The Secret is written in the namespace of the `claimer`.
- The `connection` is defined once the database, the user and the extensions exist and the server accepts connections.
- A failure never changes or removes an existing database.
- No `claim` from a project is bound to the database server OKDP uses for its own services, through a `provisioner` or a `connection`.

## 5. Removal

- When a `claim` is removed, its user can no longer log in and its Secret is removed. The database and its data are kept.
- A database with no `claim` left is kept on the server. It goes to a new `claim` only if that `claim` names it.
- If a `claim` names no database and the name chosen by the `provisioner` is a kept database, the `claim` is not bound and the reason names the database.

