# Wuthering Veil

Linux system monitoring and security operations platform.

**Stack:** C++17 agent · Rust server · Tauri desktop app (React + native window)

**Production deployment:** see [`deploy/DEPLOY.md`](deploy/DEPLOY.md)

**Install on a new machine:** see [`INSTALL.md`](INSTALL.md) — public [GitHub Releases](https://github.com/badrust0/wutheringveil/releases) ship agent + desktop (v1.0.1+). End-user docs: [`release/public/`](release/public/).

**Cut a release (maintainers):** see [`release/README.md`](release/README.md)

---

## Quick start — desktop app (dev mode)

Requires: `build-essential cmake` (C++ agent), `@tauri-apps/cli` (Tauri).

```bash
sudo apt-get install -y build-essential cmake libwebkit2gtk-4.1-dev \
  libgtk-3-dev libayatana-appindicator3-dev librsvg2-dev

# Terminal 1 — desktop app (starts embedded server + opens native window)
cd desktop
npm install
npm run tauri dev

# Terminal 2 — C++ agent (monitors this machine, pushes to embedded server)
cd agent && ./build.sh
./build/wv-agent --server http://127.0.0.1:18080 --key dev-agent-key
```

Login: `admin` / `wv` — change in **Settings** (desktop release) or via `WV_ADMIN_PASSWORD` env (dev)

The app opens a native window. The embedded Rust server runs inside it on port `18080`.
Data is stored in `~/.local/share/wuthering-veil/wv.db`.
Credentials (including agent key) are in `~/.local/share/wuthering-veil/secrets.env`.

---

## Production build

```bash
cd desktop
npm run tauri build
# → desktop/src-tauri/target/release/bundle/
```

Produces a `.deb`, `.AppImage`, or platform-native installer.

---

## Standalone web server (optional, for Docker/server deployments)

The Rust server can also run headlessly — agents push to it, any browser connects.

```bash
# Terminal 1
cd server && cargo run

# Terminal 2
cd web && npm install && npm run dev
# Open http://localhost:5173

# Terminal 3
cd agent && ./build.sh && ./build/wv-agent
```

Docker Compose (server + nginx SPA):
```bash
make release-v1                  # v1.0 production deploy (HTTP)
make release-v1-tls              # v1.0 with automatic TLS
# or manually:
cd deploy && ./release-v1.sh
```

See [`deploy/DEPLOY.md`](deploy/DEPLOY.md) for HTTPS, agents, and firewall details.

---

## Architecture

```
agent/      C++17 — procfs, netlink, inotify FIM (Phase 2)
server/     Rust  — axum API, ingest, SSE stream, alert engine (also a library)
desktop/    Tauri — native window wrapping React UI + embedded server
web/        React — standalone web mode (same UI, used by Docker deploy)
deploy/     Docker Compose, nginx config
```

## Phase roadmap

| Phase | Status   | Features                                              |
|-------|----------|-------------------------------------------------------|
| 1     | **Done** | Metrics agent, fleet dashboard, alerts, auth          |
| 2     | Planned  | Log ingest, FIM, fail2ban, SSH brute-force rules      |
| 3     | Planned  | auditd, MITRE ATT&CK mapping, active response         |
| 4     | Planned  | eBPF probes via libbpf                                |
