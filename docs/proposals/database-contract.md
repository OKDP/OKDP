# Database contract

> Status: **DRAFT**. Builds on the OKDP packaging contract, not published yet: references `Pn.n`,
> the instance states (`failed`, `pending`, `not-converged`…) and the `x-okdp-*` keywords come from
> it.

## 1. Purpose

Services installed by OKDP store their state in databases. This contract sets how a package
declares the databases it needs, how an instance gets them, and what the platform guarantees about
them, whatever deploys the server and whatever engine applies the instance.

- **The platform provisions, packages consume.** A package declares what it needs; the platform
  creates the database, the role and the credentials, and gives them as a connection. No package
  implements provisioning.
- **One consumer, one database.** Each consumer gets its own database, its own role and its own
  credentials, never those of the server.
- **Data outlives the consumer.** Removing a consumer never deletes its data (P6.6).
- **The server is a choice of the installation, not of the package.** The same package runs on a
  server shared by the project, on a dedicated server, or on a database declared by hand.
- **Independent of the database engine.** The contract names no database engine except in the
  sections and tables marked per database engine. Adding a database engine adds rows and a server
  package; it changes no consumer package.

## 2. Terms

| Term | Meaning |
|---|---|
| Engine | What turns a package and its configuration into Kubernetes resources, and keeps them in that state (packaging contract) |
| Database engine | The database software: `postgresql` or `mariadb`. Adding one is a change of this contract |
| Server | An instance providing a server capability (3.5) |
| Project server | A server of a project |
| Dedicated server | A project server used by a single consumer. It is a use, not a type |
| Platform server | The server of the platform services; never used by a project |
| Consumer | An instance consuming a `database-server` connection |
| Database binding | How the instance declaration satisfies one consumed `database-server` connection: **existing** (a connection named by reference) or **provisioned** (a database created on a server for this binding) |
| Provisioned database | The database, role, credentials Secret and connection created for one provisioned binding |
| Kept database | A provisioned database whose consumer is gone, kept on its server (D5.1) |

The key words **MUST**, **SHOULD** and **MAY** are to be interpreted as described in RFC 2119.

## 3. Model

### 3.1 Declaring the need (descriptor)

A consumed connection of contract `database-server` MAY carry `database`, the requirements of the
package. All keys are optional.

```yaml
connections:
  consumes:
    - name: db
      contract: database-server
      version: "1"
      description: Database hosting the Hive metastore schema
      database:
        engines: [postgresql, mariadb]  # omitted: any database engine
        postgresql:
          version: ">=15"               # constraint on the server version (syntax of P2.3)
          extensions: [pg_trgm]         # created in the database before the consumer starts
        mariadb:
          version: ">=10.6"
```

- A section named after a database engine holds the requirements for that database engine. It is
  allowed only for a database engine listed in `engines`, or when `engines` is omitted.
- Keys of a section: `version` for every database engine; `extensions` for `postgresql`.
- The database name, the role and the schemas are not requirements: the platform names the first
  two, and the consumer owns its database, so it creates its own schemas.

### 3.2 Binding (instance declaration)

The instance declaration gains `databases`: for each consumed `database-server` connection to
provision, the server that hosts it.

```yaml
databases:
  db:
    server: postgresql          # a server instance of the same project
```

- A consumed connection listed in `databases` is **provisioned**. The engine sets the parameter
  marked `x-okdp-connection` for it to the name of the provisioned connection; the declaration does
  not set that parameter.
- A consumed connection not listed is **existing**: its parameter names a connection, as for any
  other contract.
- An optional connection MAY be provisioned. Left empty and not listed, it binds nothing (P5.4).

### 3.3 Provisioned database

| Element | Value |
|---|---|
| Database | owned by the role; name derived from the namespace, the instance and the connection name, stable and unique on the server; a name longer than the database engine allows is truncated and ends with a stable hash of the full name |
| Role | login role owning the database, nothing else (D3.1) |
| Secret | in the namespace of the consumer (D3.2) |
| Connection | `database-server`, in the namespace of the consumer, named `<instance>-<connection>` |

### 3.4 Connection contracts

**`database-server`**, consumed by packages.

| Value | Meaning |
|---|---|
| `engine` | database engine |
| `driver` | client driver class, for the consumers that need it |
| `host`, `port` | address of the server |
| `dbName` | name of the database |
| `secretRef` | Secret with keys `username`, `password`, and `uri` when the database engine has a row in the table below |
| `sslMode` | optional, `postgresql`: transport setting of a server reached over an untrusted network |
| `tls` | optional, `mariadb`: same |
| `version` | optional: version of the server, as numbered by the database engine (`18`, `11.4`) |
| `postgresql.extensions` | optional: extensions created in the database |

The role of `secretRef` holds no administration privilege on the server (D3.1).

Syntax of `uri`, per database engine. User and password are percent-encoded.

| Database engine | `uri` |
|---|---|
| `postgresql` | `postgresql://<username>:<password>@<host>:<port>/<dbName>`, with `sslmode=<sslMode>` as a query parameter when `sslMode` is set |

**`database-server-admin`** (new). Published by a server, consumed by the platform for provisioning,
by no package.

| Value | Meaning |
|---|---|
| `engine` | database engine of the server |
| `host`, `port` | address of the server |
| `version` | version of the server, as numbered by the database engine |
| `adminSecretRef` | Secret with keys `username`, `password`, of a role that can create databases, roles and what the section of the database engine lists |
| `postgresql.extensions` | extensions the server can create |

### 3.5 Capabilities

| Capability | Level, scope, cardinality | Interface |
|---|---|---|
| `okdp.io/platform-database` | platform, `system-wide`, `one-per-cluster` | `database-server`, one connection per database listed by the administrator |
| `okdp.io/postgresql` | service, `project`, `many` | `database-server-admin` |

A server capability is a capability whose interface is `database-server-admin`, or
`okdp.io/platform-database`. Another database engine comes as another capability with the same
interface.

### 3.6 Instance status

The status of a consumer gains, for each provisioned binding: server, database name, role name,
connection name.

## 4. Responsibilities

| Guarantee | Primary owner | Also involved |
|---|---|---|
| Declared requirements | Package and its CI | Engine: checks them against the binding |
| Provisioning, credentials, removal | Engine | Server: publishes `database-server-admin` |
| Platform server kept private | Engine | Control plane: never offers it to a project |

## 5. Requirements

### 5.1 Declaration

**D1.1 — Database through a connection** (MUST, package). A package that needs a database consumes
one `database-server` connection per database. It never deploys a database server: only a package
providing a server capability does. *Test: rendered package that provides no server capability and
creates a database server → CI fails.*

**D1.2 — The admin interface is the platform's** (MUST, package CI and engine). No package consumes
`database-server-admin`. *Test: descriptor consuming `database-server-admin` → CI fails, the engine
rejects.*

**D1.3 — Requirements checked** (MUST, engine). An instance is not deployed if a bound
`database-server` connection, existing or provisioned, does not meet the `database` of its consumed
entry: its `engine` not in `engines`, or a key of the section of that database engine not met
(`version` outside the constraint, a listed extension missing from `extensions`). A requirement on a
value the connection leaves empty is not met. For a provisioned binding, the check runs against the
server before anything is created. *Test: `postgresql.version: ">=16"` on a version 15 server →
`failed` / `connection-requirements`, naming the value, no database created; existing connection
without `version` → same; `engines: [postgresql]` bound to a `mariadb` connection → same.*

### 5.2 Provisioning

**D2.1 — Provisioned at install** (MUST, engine). For each provisioned binding, the database, the
role, the requirements of the section of the database engine, the Secret and the connection exist
before any workload of the consumer starts. The consumer is `pending` / `waiting-dependency` while
the server is not ready. A failure never changes nor drops an existing database. *Test: server and
consumer declared at once on an empty project → both converge without intervention (P6.8); a
failure → `failed` / `provisioning-failed`, naming the server and the database, and every other
database of the server unchanged.*

### 5.3 Credentials and isolation

**D3.1 — Own role** (MUST, engine). The role owns its database and holds no privilege to administer
the server: no creation of databases or roles, no bypass of access controls, no replication. It
cannot connect to any other database of the server. Per database engine:

| Database engine | Forbidden on the role, including through membership or `PUBLIC` |
|---|---|
| `postgresql` | `SUPERUSER`, `CREATEDB`, `CREATEROLE`, `REPLICATION`, `BYPASSRLS`; `CONNECT` on any other database |

*Test: with the credentials of the connection, create a schema and a table → accepted; connect to
another database of the server, or grant itself a forbidden privilege → refused.*

**D3.2 — Credentials** (MUST, engine). The Secret lives in the namespace of the consumer and holds
`username`, `password` and `uri`. *Test: the three keys exist; `uri` connects.*

**D3.3 — Server credentials stay with the server** (MUST, engine). The Secret of `adminSecretRef`
is never copied into a consumer namespace, a connection a package consumes, or a parameter.
*Test: after provisioning, no object readable by the consumer holds the admin credentials.*

**D3.4 — Platform server kept private** (MUST, engine and control plane). No `project` instance
binds a connection of the platform server or provisions on it. Its connections live in its own
namespace only. *Test: project instance with `databases.db.server` naming the platform server, or
naming one of its connections → rejected; the project form never lists them.*

### 5.4 Servers

**D4.1 — One package for every project server** (MUST, package). A project server, shared by the
project or dedicated to one consumer, is an instance of the same package. *Test: the same package version serves one consumer, then several.*

**D4.2 — Light by default** (SHOULD, package). A project server runs one instance without backup by
default. *Test: install without parameters → one instance, no backup.*

**D4.3 — Health** (MUST, package). A server measures its health as a server (P7.4) and publishes
`database-server-admin` only once it accepts connections. *Test: server not accepting connections →
no `database-server-admin` connection, health not `healthy`.*

### 5.5 Lifecycle and data

**D5.1 — Uninstall of a consumer** (MUST, engine). The role can no longer connect; the Secret and
the connection are deleted; the database and its content are kept. *Test: uninstall → no login
possible, no Secret, no connection, the database and its tables still exist.*

**D5.2 — A kept database is never reused silently** (MUST, engine). A provisioned binding whose
database name matches a kept database is `failed` with the reason `database-kept`, naming the
database. *Test: uninstall, then reinstall under the same name on the same server → `failed` /
`database-kept`, the kept database unchanged.*

## 6. Changes to the other documents

| Document | Change |
|---|---|
| Packaging contract, instance declaration | `databases` (3.2) |
| Packaging contract, instance status | 3.6 |
| Packaging contract, P7.1 | Reasons of `failed`: `connection-requirements`, `provisioning-failed`, `database-kept` |
| Descriptor | `connections.consumes[].database`, allowed only on `database-server` |
| Registry | Interfaces of 3.5 |
| Connection contracts | `database-server` and `database-server-admin` as in 3.4 |

## 7. Limits of this version

- Only `postgresql` is provisioned; `mariadb` is used through existing bindings. Provisioning a
  database engine adds its rows to the tables of 3.4 and D3.1, its keys to the sections of 3.1, and
  a server package.
- Nothing prevents a second consumer from using a server meant to be dedicated.
- A kept database is dropped, or given back to a consumer, by an administrator of the server,
  outside this contract.
- The platform services keep the databases listed by the administrator on the platform server.
- Servers outside the cluster are used through existing bindings only.

## 8. Open questions

1. Should the platform services move to provisioned bindings on the platform server, ending the
   list written by the administrator?

## 9. Informative annex: reference platforms

No requirement depends on their mechanisms.

| Need | Service Binding for Kubernetes | Crossplane provider-sql | CloudNativePG | Heroku Postgres |
|---|---|---|---|---|
| Credentials given to the consumer | Secret with well-known entries: `type`, `host`, `port`, `uri`, `username`, `password` | connection Secret per role | Secret per managed role | `DATABASE_URL` set on the app |
| Database and role as separate objects | — | `Database`, `Role`, `Grant`, `Extension`, for several database engines | `Database`; `DatabaseRole` from 1.30, or roles in the cluster spec | — |
| What deletion does to data | — | `deletionPolicy`: `Delete` or `Orphan` | reclaim policy: `retain` (default) or `delete` | destroying the add-on deletes the database |

Sources: [Service Binding specification](https://servicebinding.io/spec/core/1.1.0/),
[provider-sql](https://github.com/crossplane-contrib/provider-sql),
[CloudNativePG declarative databases](https://cloudnative-pg.io/documentation/current/declarative_database_management/),
[CloudNativePG declarative roles](https://cloudnative-pg.io/docs/1.30/declarative_role_management),
[Heroku Postgres](https://devcenter.heroku.com/articles/heroku-postgresql).
