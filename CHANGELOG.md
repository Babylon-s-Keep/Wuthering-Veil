# Changelog

All notable **public** releases. Binaries: [GitHub Releases](https://github.com/badrust0/wutheringveil/releases).

## [1.0.1] — 2026-07-24

### Desktop (new on Releases)
- **`wuthering-veil-desktop-1.0.1-linux-amd64.AppImage`** and **`.deb`** — admin workstation app with embedded server
- **Settings** — change dashboard username, password, and agent API key in the app (sidebar → Settings)
- Credentials file: `~/.local/share/wuthering-veil/secrets.env` (random agent key on first launch)
- Restart required after saving credential changes
- Command Center empty state shows install steps and your agent key

### Agent
- **`wv-agent-1.0.1-linux-amd64.tar.gz`** — rebuilt for 1.0.1; same install flow as 1.0.0

### Release metadata
- `checksums.sha256` and `manifest.json` on Releases
- Public release ships **binaries only** (no source or docker-compose tarballs on the release page)

[1.0.1]: https://github.com/badrust0/wutheringveil/releases/tag/v1.0.1

---

## [1.0.0] — 2026-07-12

First public release.

### Agent (`wv-agent-1.0.0-linux-amd64.tar.gz`)
- C++17 host daemon — CPU, memory, disk, network, processes, services, listening ports
- **Twelve security event types** — failed login, privilege escalation, new listening port, outbound volatility, data exfiltration heuristics, DNS failures, suspicious process, process lineage, cryptomining, file integrity, persistence changes, unsigned modules, policy disabled
- HTTPS ingest (`X-WV-Key`); configurable poll interval
- systemd unit + **`install.sh`** in the tarball (no git clone on monitored hosts)
- Optional `.so` plugins (`/etc/wv/plugins`)

### Dashboard & control plane (included in desktop 1.0.1+)
- Rust/Axum API — host registry, alerts, live SSE stream
- React **Security Operation Center** UI — Command Center, host detail, alerts, website checks
- SIEM investigation view — search security events, MITRE tactic filters, retention stats
- Health check: `GET /api/v1/health`

### Documentation
- Install guide, version file, checksums on Releases

### Not on public Releases in 1.0.0
- Desktop `.deb` / AppImage — **added in 1.0.1**
- Pre-built server Docker images — fleet operators deploy server stack separately today

### Known limitations
- Dashboard session token does not expire; single admin user; no RBAC
- Desktop embedded server listens on `127.0.0.1:18080` only
- Change default login (`admin` / `wv`) after install — use **Settings** on desktop 1.0.1+

[1.0.0]: https://github.com/badrust0/wutheringveil/releases/tag/v1.0.0
