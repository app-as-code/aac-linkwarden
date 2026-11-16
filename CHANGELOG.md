# Changelog

All notable changes to this deployment definition are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Changed

- Upstream image tracks Linkwarden v2.16.3 as defined in `service.yml`.

## [0.0.1]

### Added

- Initial App-as-Code service definition for Linkwarden with a PostgreSQL 18 dependency.
- Web interface on port 3000 with Traefik routing and automatic DNS.
- Optional OpenID Connect login (Authentik) alongside credential login.
- README with configuration reference and project icon.
