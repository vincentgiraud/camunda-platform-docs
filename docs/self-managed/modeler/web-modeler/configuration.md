---
id: configuration
title: Configuration
description: "Read details on the configuration variables of Web Modeler Self-Managed, including components such as REST API, Identity, Keycloak, webapp, and WebSocket."
---

:::caution Beta Offering
Web Modeler Self-Managed is currently offered as a [beta release](../../../../reference/early-access#beta)
with limited availability for enterprise customers only. It is not recommended for production use and there is no maintenance service guaranteed.
Special [terms & conditions](https://camunda.com/legal/terms/camunda-platform/camunda-platform-8-self-managed/) apply.
However, we encourage you to provide feedback via your designated support channel or the [Camunda Forum](https://forum.camunda.io/).
:::

The different components of Web Modeler Self-Managed can be configured using environment variables. Each component's variables are described below.
For a working example configuration, that shows how the components are correctly wired together, see the [Docker Compose file for Web Modeler](../../../platform-deployment/docker#web-modeler-1).

### Configuration of the `restapi` component

#### Database

Web Modeler requires a PostgreSQL database as persistent data storage (other database systems are currently not supported.)

| Environment variable  | Description            | Example value          |
| --------------------- | ---------------------- | ---------------------- |
| `RESTAPI_DB_HOST`     | Database host name     | `postgres.example.com` |
| `RESTAPI_DB_PORT`     | Database port          | `5432`                 |
| `RESTAPI_DB_NAME`     | Database name          | `modeler-db`           |
| `RESTAPI_DB_USER`     | Database user name     | `modeler-user`         |
| `RESTAPI_DB_PASSWORD` | Database user password | \*\*\*                 |

#### SMTP / email

Web Modeler requires an SMTP server to send notification emails to users.

| Environment variable        | Description                                                                                                                                                                                         | Example value                 | Default value |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------- | ------------- |
| `RESTAPI_MAIL_HOST`         | SMTP server host name                                                                                                                                                                               | `smtp.example.com`            | -             |
| `RESTAPI_MAIL_PORT`         | SMTP server port                                                                                                                                                                                    | `587`                         | -             |
| `RESTAPI_MAIL_USER`         | [optional]<br/>SMTP user name                                                                                                                                                                       | `modeler-user`                | -             |
| `RESTAPI_MAIL_PASSWORD`     | [optional]<br/>SMTP user password                                                                                                                                                                   | \*\*\*                        | -             |
| `RESTAPI_MAIL_ENABLE_TLS`   | Enforce TLS encryption for SMTP connections (using STARTTLS).                                                                                                                                       | `true`                        | `true`        |
| `RESTAPI_MAIL_FROM_ADDRESS` | Email address used as the sender of emails sent by Web Modeler.                                                                                                                                     | `noreply@example.com`         | -             |
| `RESTAPI_MAIL_FROM_NAME`    | [optional]<br/>Name displayed as the sender of emails sent by Web Modeler.                                                                                                                          | `Camunda`                     | `Camunda`     |
| `RESTAPI_SERVER_URL`        | URL at which users access Web Modeler in the browser (used to construct links in notification emails).<br/>_Note_: Use the domain root; running Web Modeler on a context path is not yet supported. | `https://modeler.example.com` | -             |

#### WebSocket

The `restapi` component sends certain events (e.g. "file updated", "comment added") to the [WebSocket](#configuration-of-the-websocket-component) server.

| Environment variable    | Description                                                                            | Example value        |
| ----------------------- | -------------------------------------------------------------------------------------- | -------------------- |
| `RESTAPI_PUSHER_HOST`   | [Internal](#notes-on-host-names-and-port-numbers) host name of the WebSocket server.   | `modeler-websockets` |
| `RESTAPI_PUSHER_PORT`   | [Internal](#notes-on-host-names-and-port-numbers) port number of the WebSocket server. | `8060`               |
| `RESTAPI_PUSHER_APP_ID` | _must be the same as_ [`PUSHER_APP_ID`](#configuration-of-the-websocket-component)     | `web-modeler`        |
| `RESTAPI_PUSHER_KEY`    | _must be the same as_ [`PUSHER_APP_KEY`](#configuration-of-the-websocket-component)    | \*\*\*               |
| `RESTAPI_PUSHER_SECRET` | _must be the same as_ [`PUSHER_APP_SECRET`](#configuration-of-the-websocket-component) | \*\*\*               |

#### Identity / Keycloak

Web Modeler integrates with Identity and Keycloak for authentication and authorization (using OAuth 2.0 + OpenID Connect) as well as user management.

| Environment variable                      | Description                                                                                                                                                                                                                                                    | Example value                                               |
| ----------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------- |
| `RESTAPI_OAUTH2_TOKEN_ISSUER`             | URL of the token issuer (used for JWT validation).                                                                                                                                                                                                             | `https://keycloak.example.com/auth/realms/camunda-platform` |
| `RESTAPI_OAUTH2_TOKEN_ISSUER_BACKEND_URL` | [optional]<br/>[Internal](#notes-on-host-names-and-port-numbers) URL used to request Keycloak's [OpenID Provider Configuration](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig); if not set, `RESTAPI_OAUTH2_TOKEN_ISSUER` is used. | `http://keycloak:8080/auth/realms/camunda-platform`         |
| `RESTAPI_IDENTITY_BASE_URL`               | [Internal](#notes-on-host-names-and-port-numbers) base URL of the Identity API (used to fetch user data).                                                                                                                                                      | `http://identity:8080`                                      |

### Configuration of the `webapp` component

#### General

| Environment variable      | Description                                                                                                               | Example value                 | Default value |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------- | ----------------------------- | ------------- |
| `SERVER_URL`              | URL at which users access Web Modeler in the browser.                                                                     | `https://modeler.example.com` | -             |
| `SERVER_HTTPS_ONLY`       | Enforce the usage of HTTPS when users access Web Modeler (by redirecting from `http://` to `https://`).                   | `true`                        | `false`       |
| `RESTAPI_HOST`            | [Internal](#notes-on-host-names-and-port-numbers) host name of the `restapi` application.                                 | `modeler-restapi`             | -             |
| `RESTAPI_PORT`            | [Internal](#notes-on-host-names-and-port-numbers) port number on which the `restapi` serves the regular API endpoints.    | `8081`                        | `8081`        |
| `RESTAPI_MANAGEMENT_PORT` | [Internal](#notes-on-host-names-and-port-numbers) port number on which the `restapi` serves the management API endpoints. | `8091`                        | `8091`        |

#### Identity / Keycloak

| Environment variable    | Description                                                                                                               | Example value                                                                     |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| `OAUTH2_CLIENT_ID`      | Client ID of the Web Modeler application configured in Identity;<br/>_must be set to_ `web-modeler`.                      | `web-modeler`                                                                     |
| `OAUTH2_TOKEN_AUDIENCE` | Expected token audience (used for JWT validation);<br/>_must be set to_ `web-modeler`.                                    | `web-modeler`                                                                     |
| `OAUTH2_TOKEN_ISSUER`   | URL of the token issuer (used for JWT validation).                                                                        | `https://keycloak.example.com/auth/realms/camunda-platform`                       |
| `KEYCLOAK_BASE_URL`     | Base URL of the Keycloak instance.                                                                                        | `https://keycloak.example.com`                                                    |
| `KEYCLOAK_REALM`        | Keycloak realm used for Camunda Platform.                                                                                 | `camunda-platform`                                                                |
| `KEYCLOAK_JWKS_URL`     | [Internal](#notes-on-host-names-and-port-numbers) URL used to request Keycloak's JSON Web Key Set (for JWT verification). | `http://keycloak:8080/auth/realms/camunda-platform/protocol/openid-connect/certs` |
| `IDENTITY_BASE_URL`     | [Internal](#notes-on-host-names-and-port-numbers) base URL of the Identity API (used to fetch user data).                 | `http://identity:8080`                                                            |

#### WebSocket

The [WebSocket](https://en.wikipedia.org/wiki/WebSocket) server shipped with Web Modeler Self-Managed is based on the [laravel-websockets](https://laravel.com/docs/9.x/broadcasting#open-source-alternatives-php) open source package and implements the [Pusher Channels Protocol](https://pusher.com/docs/channels/library_auth_reference/pusher-websockets-protocol/).

The `webapp` component sends certain events (e.g. "user opened diagram", "user left diagram") to the [WebSocket server](#configuration-of-the-websocket-component) and can also react to such events (e.g. show a notification in the UI that a user left the diagram).

| Environment variable      | Description                                                                                                                                   | Example value        | Default value |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- | -------------------- | ------------- |
| `PUSHER_HOST`             | [Internal](#notes-on-host-names-and-port-numbers) host name of the WebSocket server.                                                          | `modeler-websockets` | -             |
| `PUSHER_PORT`             | [Internal](#notes-on-host-names-and-port-numbers) port number of the WebSocket server.                                                        | `8060`               | -             |
| `PUSHER_APP_ID`           | _must be the same as_ [`PUSHER_APP_ID`](#configuration-of-the-websocket-component)                                                            | `web-modeler`        | -             |
| `PUSHER_KEY`              | _must be the same as_ [`PUSHER_APP_KEY`](#configuration-of-the-websocket-component)                                                           | \*\*\*               | -             |
| `PUSHER_SECRET`           | _must be the same as_ [`PUSHER_APP_SECRET`](#configuration-of-the-websocket-component)                                                        | \*\*\*               | -             |
| `CLIENT_PUSHER_HOST`      | [External](#notes-on-host-names-and-port-numbers) host name on which the Web Modeler client accesses the WebSocket server from the browser.   | `ws.example.com`     | -             |
| `CLIENT_PUSHER_PORT`      | [External](#notes-on-host-names-and-port-numbers) port number on which the Web Modeler client accesses the WebSocket server from the browser. | `443`                | -             |
| `CLIENT_PUSHER_KEY`       | _must be the same as_ [`PUSHER_APP_KEY`](#configuration-of-the-websocket-component)                                                           | \*\*\*               | -             |
| `CLIENT_PUSHER_FORCE_TLS` | Enable TLS encryption for WebSocket connections initiated by the browser.                                                                     | `true`               | `false`       |

### Configuration of the `websocket` component

| Environment variable | Description                                                                                              | Example value |
| -------------------- | -------------------------------------------------------------------------------------------------------- | ------------- |
| `PUSHER_APP_ID`      | ID of the single application/tenant configured for Web Modeler.                                          | `web-modeler` |
| `PUSHER_APP_KEY`     | A unique key used for authentication. Provide a random alphanumeric string of at least 20 characters.    | \*\*\*        |
| `PUSHER_APP_SECRET`  | A unique secret used for authentication. Provide a random alphanumeric string of at least 20 characters. | \*\*\*        |

### Notes on host names and port numbers

- _Internal_ refers to host names and port numbers that are only used inside a Docker Compose network or Kubernetes cluster for backend-to-backend communication.
- _External_ refers to host names and port numbers that are exposed to the outside and can be reached from a web browser.
