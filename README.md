<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/icon-dark.svg">
    <img src="assets/icon.svg" alt="Linkwarden logo" width="128">
  </picture>
</p>

<h1 align="center">aac-linkwarden</h1>

<p align="center">Self-hosted deployment definition for Linkwarden, a self-hosted bookmark manager.</p>

<p align="center">
  <a href="https://github.com/linkwarden/linkwarden"><img alt="Upstream" src="https://img.shields.io/badge/upstream-linkwarden%2Flinkwarden-blue?logo=github"></a>
  <img alt="Image" src="https://img.shields.io/badge/image-ghcr.io%2Flinkwarden%2Flinkwarden%3Av2.16.3-2496ED?logo=docker&logoColor=white">
  <a href="https://github.com/linkwarden/linkwarden/blob/main/LICENSE"><img alt="License" src="https://img.shields.io/badge/license-AGPL--3.0-green"></a>
</p>

## About

Linkwarden is a self-hosted bookmark manager that lets you save, organize and search links
in one place. Besides plain bookmarks it keeps a local copy of each saved page (readable
text, screenshot and PDF), so content stays available even when the original site goes
offline. Bookmarks can be grouped into collections and tagged, and collections can be shared
with other users.

Key features (as provided by upstream):

- Bookmarks organized in collections and tags, with full-text search
- Automatic archiving of saved pages (readable text, screenshot, PDF)
- Multi-user support with shared collections
- Browser extensions and a mobile-friendly web interface
- Login via credentials or OpenID Connect (this deployment is prepared for Authentik)

## Upstream

- Project: <https://github.com/linkwarden/linkwarden>
- Website: <https://linkwarden.app>
- Container image: `ghcr.io/linkwarden/linkwarden:v2.16.3`
- Database: `postgres:18` (deployed as `aac-linkwarden-db`)
- License: AGPL-3.0 (upstream project)

## Quick start (Docker Compose)

The Compose file is rendered from `service.yml` by the App-as-Code template engine. For a
manual run, the external networks used for routing must exist first:

```bash
docker network create services-secured
docker network create services-exposed

# Provide the values listed under "Configuration" in .env / stack.env
docker compose up -d
```

Secrets are not stored in this repository. Provide them through the deployment pipeline
or create the `.env` and `stack.env` files yourself (see below). Do not commit them.

## Configuration

Environment variables (defaults from `service.yml`; `CHANGE_ME` must be set per deployment):

| Variable | Default | Description |
|---|---|---|
| `NEXTAUTH_URL` | `CHANGE_ME` | Public URL of the Linkwarden instance, e.g. `https://links.example.com` |
| `NEXTAUTH_SECRET` | `CHANGE_ME` (secret) | Secret used to sign session tokens |
| `DATABASE_URL` | `CHANGE_ME` (secret) | PostgreSQL connection string, e.g. `postgresql://linkwarden:<password>@aac-linkwarden-db:5432/linkwarden` |
| `POSTGRES_USER` | `linkwarden` (secret) | Database user for the `postgres` dependency |
| `POSTGRES_DB` | `linkwarden` (secret) | Database name |
| `POSTGRES_PASSWORD` | `CHANGE_ME` (secret) | Database password |
| `NEXT_PUBLIC_CREDENTIALS_ENABLED` | `true` | Allow login with username and password |
| `NEXT_PUBLIC_DISABLE_REGISTRATION` | `false` | Disable self-registration |
| `NEXT_PUBLIC_AUTHENTIK_ENABLED` | `true` | Show the OpenID Connect (Authentik) login |
| `AUTHENTIK_ISSUER` | `CHANGE_ME` | OIDC issuer URL of the identity provider |
| `AUTHENTIK_CLIENT_ID` | `CHANGE_ME` | OIDC client ID |
| `AUTHENTIK_CLIENT_SECRET` | `CHANGE_ME` (secret) | OIDC client secret |
| `AUTHENTIK_CUSTOM_NAME` | `Authentik` | Label of the SSO login button |
| `DISABLE_NEW_SSO_USERS` | `false` | Block account creation through SSO |
| `HOMEPAGE_WIDGET_KEY` | `CHANGE_ME` (secret) | API key for the Homepage dashboard widget |
| `STORAGE_FOLDER` | `/data` | Storage path inside the container for archived content |
| `RE_ARCHIVE_LIMIT` | `5` | Maximum number of re-archive attempts per link |
| `PAGINATION_TAKE_COUNT` | `50` | Number of items loaded per page |
| `AUTOSCROLL_TIMEOUT` | `30` | Autoscroll timeout (seconds) used during archiving |
| `PUID` / `PGID` | `1000` | User and group ID the container runs as |
| `TZ` | `Europe/Berlin` | Time zone |

Ports:

| Port | Protocol | Purpose |
|---|---|---|
| `3000` | TCP | Web interface (`web`) |

Volumes:

| Mount | Description |
|---|---|
| `config` -> `/config` | Linkwarden application configuration |
| `db` -> `/var/lib/postgresql` | PostgreSQL data (database service) |

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, ports, environment,
   volumes, dependencies and per-stage overrides. Do not edit the rendered output.
2. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
3. Releases of this definition are published under <https://github.com/app-as-code/aac-linkwarden>.

## Credits and license

Linkwarden is developed by the Linkwarden project and licensed under AGPL-3.0. This
repository only contains the deployment definition for it and is not a fork of the
application. The logo is provided by the Linkwarden project; see [`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md)
for icon sources and licenses.
