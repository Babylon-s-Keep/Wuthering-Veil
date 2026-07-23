# Changelog

All notable releases of Wuthering Veil.

## [1.0.1] — 2026-07-24

### Desktop
- **Settings** page — change dashboard username, password, and agent API key in-app
- Credentials persisted in `~/.local/share/wuthering-veil/secrets.env` (random agent key on first launch)
- Restart required after credential changes

### Releases
- Public binaries: agent tarball, desktop `.deb` + AppImage (v1.0.1)
- `PUBLIC_RELEASE=1` build excludes source/docker-compose tarballs from public uploads

[1.0.1]: https://github.com/badrust0/wutheringveil/releases/tag/v1.0.1

## [1.0.0] — 2026-07-11

First production release.

### Control plane
- Rust server (`wv-server`) with Axum API, SQLite host registry, in-memory metrics/alerts
- React dashboard (web + Tauri desktop) — Security Operation Center branding
- Docker Compose stack: `wv-server` + `wv-web` (nginx) + optional `wv-caddy` (TLS)
- Production mode (`WV_PRODUCTION=1`) rejects weak default credentials
- Health endpoint returns JSON with version: `GET /api/v1/health`

### Agent
- C++17 `wv-agent` — metrics, 12 security signals, remote commands, plugin ABI
- HTTPS ingest via OpenSSL; systemd unit + `install-agent.sh`

### Operations
- `deploy/release-v1.sh` — one-command v1.0 deployment
- `deploy/setup-prod.sh` — auto-generated secrets
- `deploy/DEPLOY.md`, `INSTALL.md`, product + developer PDF guide

### Known limitations (Phase 2)
- Dashboard token does not expire; no RBAC
- Metrics/alerts mostly in-memory (SQLite stores hosts only)
- Three security matrix UI labels not yet emitted by agent

[1.0.0]: https://github.com/badrust0/wutheringveil/releases/tag/v1.0.0
