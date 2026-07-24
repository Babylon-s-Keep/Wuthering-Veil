# Wuthering Veil

**Security Operation Center for Linux fleets**

`v1.0.1` · C++17 agent · Rust/Axum · React · Tauri desktop

## Documentation

| Guide | Description |
|-------|-------------|
| **[INSTALL.md](INSTALL.md)** | **Start here** — desktop app, Linux agent, credentials, troubleshooting |
| [DESKTOP.md](DESKTOP.md) | AppImage / `.deb`, Settings, local agent |
| [CHANGELOG.md](CHANGELOG.md) | Release history (1.0.0 → 1.0.1) |
| [RELEASE_NOTES.md](RELEASE_NOTES.md) | v1.0.1 summary for this release |
| [Releases](https://github.com/badrust0/wutheringveil/releases) | Download binaries + `checksums.sha256` |

---

## What it is

Wuthering Veil is a **fleet monitoring and SOC-style control plane** for Linux.

| Layer | Role |
|-------|------|
| **`wv-agent`** | C++17 daemon on each host — procfs metrics, auth.log + port/process/FIM security signals, HTTPS ingest |
| **`wv-server`** | Rust/Axum API — SQLite host registry, alert engine, SIEM event store, SSE fan-out |
| **Dashboard** | React SPA — Command Center, host detail, alerts, SIEM investigation, website checks |
| **Desktop** | Tauri app — embedded server on `127.0.0.1:18080`, native window, **Settings** for credentials |

Agents push on a configurable interval. The dashboard streams live updates over **SSE**. Operators sign in with a bearer token; agents authenticate with **`X-WV-Key`**.

This repository holds **install documentation** and links to **release binaries**. Application source is not published here.

---

## Downloads (v1.0.1)

**[github.com/badrust0/wutheringveil/releases](https://github.com/badrust0/wutheringveil/releases)**

| Artifact | Platform | Status |
|----------|----------|--------|
| `wv-agent-1.0.1-linux-amd64.tar.gz` | Linux x86_64 | **Available** |
| `wuthering-veil-desktop-1.0.1-linux-amd64.AppImage` | Linux x86_64 | **Available** |
| `wuthering-veil-desktop-1.0.1-linux-amd64.deb` | Debian/Ubuntu | **Available** |
| `checksums.sha256` | — | Verify all files |
| `manifest.json` | — | On Releases (artifact list + SHA256) |
| Server Docker images | — | Coming in a future release |

Use the **named files** above — not GitHub’s auto-generated **“Source code (zip)”**.

```bash
sha256sum -c checksums.sha256
```

---

## Quick start — Desktop

```bash
chmod +x wuthering-veil-desktop-1.0.1-linux-amd64.AppImage
./wuthering-veil-desktop-1.0.1-linux-amd64.AppImage

# or .deb
sudo dpkg -i wuthering-veil-desktop-1.0.1-linux-amd64.deb
```

1. Sign in: `admin` / `wv` (first launch — **change in Settings immediately**)  
2. **Settings** → password + agent API key  
3. Install agents — [INSTALL.md](INSTALL.md) · [DESKTOP.md](DESKTOP.md)

Credentials: `~/.local/share/wuthering-veil/secrets.env` (restart after edits)

---

## Quick start — Agent

On every Linux host you want to monitor:

```bash
tar xzf wv-agent-1.0.1-linux-amd64.tar.gz
cd wv-agent-1.0.1-linux-amd64

WV_SERVER_URL=https://your-server.example.com \
WV_API_KEY=<matches server WV_AGENT_KEY or desktop Settings> \
  sudo ./install.sh
```

**Verify**

```bash
systemctl status wv-agent
journalctl -u wv-agent -f
```

Host should appear in the dashboard within ~30s (default poll: 5s).

| Variable | Meaning |
|----------|---------|
| `WV_SERVER_URL` | Server base URL, no trailing slash |
| `WV_API_KEY` | Shared agent key on the control plane |
| `WV_INTERVAL` | Optional poll interval (seconds) |
| `WV_PLUGIN_DIR` | Optional `.so` plugin directory (default `/etc/wv/plugins`) |

---

## Install — Desktop (details)

Embedded server + native UI. Data: `~/.local/share/wuthering-veil/wv.db`

See **[DESKTOP.md](DESKTOP.md)** for AppImage, `.deb`, Settings, and local agent setup.

**Local agent** (same machine as desktop):

```bash
WV_SERVER_URL=http://127.0.0.1:18080 \
WV_API_KEY=<from Settings> \
  sudo ./install.sh
```

---

## Architecture

```
  Linux hosts                         Operator
  ┌─────────────┐                    ┌──────────────────┐
  │  wv-agent   │──HTTPS POST ingest─│  wv-server       │
  │  (C++17)    │                    │  (Rust / Axum)   │
  └─────────────┘                    │        │         │
        │                            │   React UI       │
        │  metrics + security_events │   (web / Tauri)  │
        └────────────────────────────│   SSE / REST     │
                                     └──────────────────┘
```

**Desktop mode:** server + UI in one Tauri process — embedded API `http://127.0.0.1:18080`.  
**Server mode:** multi-host fleet (Docker deploy — not yet a public binary download).

**Health check:** `GET /api/v1/health`

---

## Security signals (agent)

Twelve event types emitted today, including:

| Type | Description |
|------|-------------|
| `failed_login` | Failed SSH/auth attempts |
| `privilege_escalation` | sudo / setuid activity |
| `new_listening_port` | New open port |
| `outbound_volatility` | Unusual outbound traffic |
| `data_exfiltration` | Large outbound transfer heuristics |
| `dns_failures` | DNS resolution failures |
| `suspicious_process` | Unexpected process behaviour |
| `process_lineage` | Suspicious parent/child chain |
| `cryptomining` | Mining-related heuristics |
| `file_integrity` | FIM — watched file changed |
| `persistence_change` | Cron/unit/autostart change |
| `unsigned_module` | Unsigned kernel module loaded |
| `security_policy_disabled` | SELinux/AppArmor disabled |

Severity: `info` · `warning` · `critical`

---

## Troubleshooting

| Symptom | Likely cause |
|---------|----------------|
| `ingest failed (HTTP 401)` | `WV_API_KEY` mismatch — check desktop **Settings** or server config |
| `ingest failed (HTTP 0)` | Server down, TLS failure, or firewall |
| Host stuck offline | Agent not running or wrong `WV_SERVER_URL` |
| Dashboard empty after login | No agents connected yet — install agent tarball |
| Desktop white screen | WebKit/GPU — `WEBKIT_DISABLE_DMABUF_RENDERER=1` |
| Login fails after password change | Restart desktop app after **Settings** save |
| Forgot desktop password | Edit `secrets.env`; restart app |

More detail: [INSTALL.md](INSTALL.md)

---

## Version

[VERSION](VERSION) · [CHANGELOG.md](CHANGELOG.md) · [RELEASE_NOTES.md](RELEASE_NOTES.md) (v1.0.1 summary)

**Stack:** C++17 · Rust 2021 · Axum 0.8 · React 19 · Tauri 2 · SQLite
