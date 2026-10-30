# OpenID Connect

OKDP uses OIDC for authentication across all the platform components. By delegating identity verification to an external Identity Provider, OKDP provides a unified Single Sign-On experience for its users.
 
## OIDC - OpenID Connect
 
OpenID Connect is an identity layer built on top of the OAuth 2.0 protocol. It allows a client to authenticate a user with an identity provider and obtain verified information about the user's identity in the form of an `id_token` (JWT).
 
The entities involved are:
 
| Entities | Job |
|---|---|
| **IdProvider** | Identity provider; manages authentication and token issuance |
| **Relying Party** | An application or website that delegates the task of authenticating its users to an identity provider |
| **End-User** | A user authenticating via the IdProvider. Person using registered clients to access resources. |

## SSO - Single sign-on
 
The IdProvider acts as the project’s central authentication hub. Each application component in the system delegates its authentication to the IdProvider, which enables:

- Single sign-on (SSO) for all components;
- Centralized management of users, groups, and roles;
- Issuance of standardized tokens (JWT) that can be used by all services.

For a component to utilize this SSO mechanism, it must exist on the IdProvider side as a **client**.
 
## Dynamic Client Registration

OKDP is based on an architecture in which application components can be added dynamically, without manual intervention on the infrastructure. Each component requiring authentication must have an OpenID Connect client registered with the IdProvider.
 
Project components are not known in advance and can be added dynamically. It is not viable to create each OIDC client manually in the IdProvider administration console.
 
It is necessary for **each component to be able to register automatically** within the IdProvider during deployment, without human intervention. 

Since manually creating clients in the IdProvider is not compatible with this dynamic component-adding process, it is necessary to **automate the creation of OIDC clients** when each new component is deployed.

## DCR Protocol

A way to allow this automation is based on the **OIDC Dynamic Client Registration** protocol (DCR).
 
[DCR](https://openid.net/specs/openid-connect-registration-1_0.html) protocol is based on [RFC 7591](https://www.rfc-editor.org/rfc/rfc7591)). It allows a client to register automatically with an IdProvider via an HTTP call to a dedicated endpoint, without any prior manual configuration.
 
The identity provider exposes a **registration endpoint** that accepts a `POST` request describing the metadata for the client to be created.
 
### Authentication Flow

This document describes the process of dynamically registering a new component through the deployment pipeline, followed by its authentication with the Identity Provider.


| New component | Flux (1) | Deployment Pipeline | Flux (2) | IdProvider |
| :--- | :---: | :---: | :---: | :--- |
| | | **1.** `POST /clients-registrations/openid-connect`<br>*(client_name, redirect_uris, scope...)* | -> | |
| | | | <- | **2.** `201 Created`<br>*(client_id, client_secret, registration_access_token)* |
| | <- | **3.** OIDC Credentials Injection<br>*(client_id / client_secret)* | | |
| **4.** SSO Authentication<br>*(Authorization Code / etc.)* | -> | -> | -> | |

### Stage description
1. **OIDC Registration Request:** The deployment pipeline dynamically registers the client with IdProvider by sending its metadata (`client_name`, `redirect_uris`, `scope`, etc.).
2. **Return of credentials:** Keycloak validates the registration and returns a `201 Created` status along with the `client_id`, `client_secret`, and `registration_access_token`.
3. **Configuration injection:** The pipeline injects the generated credentials (`client_id` and `client_secret`) into the new component.
4. **SSO Authentication:** The new component is operational and can initiate SSO authentication flows (e.g., *Authorization Code Flow*) directly with the IdProvider.
 
### Client Registration Request
 
```http
POST /realms/{realm}/clients-registrations/openid-connect/ HTTP/2
Host: keycloak.example.com
Accept: */*
Content-Type: application/json
 
{
  "client_name": "components-one",
  "redirect_uris": ["https://components-one.example.com/callback"],
  "grant_types": ["authorization_code"],
  "response_types": ["code"],
  "scope": "openid profile groups"
}
```
 
Response type :
 
```json
{
  "client_id": "generated-client-id",
  "client_secret": "generated-client-secret",
  "registration_access_token": "eyJhbGciOi...",
  "registration_client_uri": "https://idprovider.example.com/realms/{realm}/clients-registrations/openid-connect/{generated-client-id}",
  "scope": "openid profile groups"
}
```
 
The `registration_access_token` returned can then be used to update or delete the client via the same endpoint (`GET`, `PUT`, `DELETE` operations).
 
### Keycloak and DCR

By default, Keycloak restricts the **client scopes** that can be assigned to a client during dynamic client registration (DCR). `Optional` or `Default` client scopes can be added without any additional options. Those of type `None` must be added to the list of `Allowed Client Scopes`. Even if the `groups` client scope exists at the realm level, a dynamically created client will not be able to use it until this scope has been explicitly authorized in the `Allowed Client Scopes`. Without this authorization, a request for the `groups` scope during DCR will be ignored or rejected. If ignored, the tokens issued for dynamically created components will not contain the `groups` claim—which is a blocking issue if the project’s components rely on groups for their authorization rules.

`Trusted Hosts` Policy must also be configured to allow the various components to register within Keycloak.

These two policies are located in the `Client Registration` tab in the `Clients` page and apply at the realm level.

## References
 
- OpenID Connect Dynamic Client Registration 1.0 — [openid.net/specs](https://openid.net/specs/openid-connect-registration-1_0.html)
- [RFC 7591](https://datatracker.ietf.org/doc/html/rfc7591) - OAuth 2.0 Dynamic Client Registration Protocol
- [RFC 7592](https://datatracker.ietf.org/doc/html/rfc7592) - OAuth 2.0 Dynamic Client Registration Management Protocol
- Keycloak - [Client Registration](https://www.keycloak.org/securing-apps/client-registration)
