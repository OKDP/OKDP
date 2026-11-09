# Object storage contract

This document defines version `1` of the `s3` contract. It follows the rules of [OKDP connection](../connection.md).

## 1. Purpose

Services store files and tables in an object store. This contract defines what a `claim` for object storage holds, what the `connection` gives, and what a `provisioner` guarantees.

An object store here is reached with the S3 API, an access key and a secret key. A `connection` gives one identity and the buckets it can use, all on the same store.

A store needs no `provisioner`: its `connection` can be declared by hand, for example for a cloud store or an existing cluster.

## 2. Connection

| Property | Meaning |
|---|---|
| `endpoint` | URL of the S3 API |
| `region` | Region used to sign the requests, for example `us-east-1` |
| `buckets` | Buckets the identity can use |
| `secretRef` | Secret holding `accessKey` and `secretKey` |
| `pathStyle` | Optional. `true`: the bucket goes in the path of the URL, not in the host name |
| `stsEndpoint` | Optional. URL where the identity gets temporary credentials. Unset: the store gives none |
| `roleArn` | Optional. Role to assume for temporary credentials, for a store that needs one |

## 3. Claim

| Requirement | Meaning | Met when |
|---|---|---|
| `buckets` | Buckets the `claimer` needs | Each one is in `buckets` |
| `sts` | The `claimer` needs temporary credentials | `stsEndpoint` is set |

- Every requirement is optional.
- A `claim` passes no other value to the `provisioner`.

## 4. Provisioner

- Each `claim` gets its own identity. The identity has every right on the objects of its buckets and none on the rest of the store: it cannot create or remove buckets, change their access rules, or reach another bucket.
- The `provisioner` creates each bucket the `claim` names that does not exist yet. If the `claim` names no bucket, it creates one, with a name that is unique on the store and always the same for the same `claim`.
- A bucket belongs to the namespace of the `claim` it was created for. A `claim` gets only buckets of its own namespace. For any other bucket, the `claim` is not bound and the reason names the bucket.
- Two `claims` that name the same bucket share it. Each one still gets its own identity.
- When the buckets of a `claim` change, the rights of its identity change with them. A bucket removed from the list is kept.
- When the `provisioner` sets `stsEndpoint`, the identity gets temporary credentials there. They never have more rights than the identity.
- The Secret is written in the namespace of the `claimer`.
- The `connection` is defined once the buckets and the identity exist and the store accepts the identity.
- A failure never changes or removes an existing bucket or its objects.

## 5. Removal

- When a `claim` is removed, its identity and the temporary credentials it got no longer work, and its Secret is removed. The buckets and their objects are kept.
- A bucket with no `claim` left is kept on the store. It goes to a new `claim` of its namespace only if that `claim` names it.
- If a `claim` names no bucket and the name chosen by the `provisioner` is a kept bucket, the `claim` is not bound and the reason names the bucket.
