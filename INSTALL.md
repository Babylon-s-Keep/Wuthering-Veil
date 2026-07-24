# Installing Wuthering Veil

Clone the repo, pick a deployment mode, install dependencies, build (or use Docker), then install agents on the hosts you want to monitor.

```bash
git clone https://github.com/badrust0/wutheringveil.git
cd wutheringveil
```

The repository contains **source code** (private dev) and **release packaging scripts**. Public users download pre-built artifacts from [GitHub Releases](https://github.com/babylon-s-keep/wutheringveil/releases) — see [`release/public/INSTALL.md`](release/public/INSTALL.md) for the end-user install guide copied to the public repo.

For production TLS, firewall rules, and ops detail, see [`deploy/DEPLOY.md`](deploy/DEPLOY.md).

---

## Install from GitHub Release (v1.0.1+)

Public release page: **https://github.com/badrust0/wutheringveil/releases**

| Artifact | Use |
|----------|-----|
| `wuthering-veil-desktop-1.0.1-linux-amd64.AppImage` / `.deb` | Admin workstation (embedded server + UI) |
| `wv-agent-1.0.1-linux-amd64.tar.gz` | Monitor a Linux host |
| `checksums.sha256` | Verify downloads |

Source and docker-compose tarballs are **not** uploaded to the public release (`PUBLIC_RELEASE=1`). Server deploy uses this private repo + Docker (see Option A below).

### Desktop (from Releases)

```bash
chmod +x wuthering-veil-desktop-1.0.1-linux-amd64.AppImage
./wuthering-veil-desktop-1.0.1-linux-amd64.AppImage
# or: sudo dpkg -i wuthering-veil-desktop-1.0.1-linux-amd64.deb
```

- Login: `admin` / `wv` on first launch  
- **Settings** (sidebar): change username, password, agent API key  
- Credentials file: `~/.local/share/wuthering-veil/secrets.env` — restart after changes  
- Full guide: [`release/public/DESKTOP.md`](release/public/DESKTOP.md)

### Agent on a Linux host (from Releases)

```bash
tar xzf wv-agent-1.0.1-linux-amd64.tar.gz
cd wv-agent-1.0.1-linux-amd64
WV_SERVER_URL=https://your-wv-server.example.com \
WV_API_KEY=<same as WV_AGENT_KEY on server, or from desktop Settings> \
  sudo ./install.sh
```

Verify: `systemctl status wv-agent`

---

## Choose a mode (build from source)

| Mode | Best for | Remote agents? | Remote dashboard? |
|------|----------|----------------|-------------------|
| **Docker Compose** | Production / team | Yes | Yes (HTTP or HTTPS) |
| **Desktop (Tauri)** | Single admin workstation | No (localhost) | Local window only |
| **Server + web (dev)** | Hacking on the stack | Yes | Yes (browser) |

The **Linux agent** (`wv-agent`) only runs on Linux. The server and web dashboard can run on any host that meets the requirements below.

---

## Option A — Production server (Docker) **recommended**

**On the monitoring server** (Ubuntu/Debian with Docker):

### Requirements

- Docker Engine + Docker Compose v2
- Ports **80** (and **443** if using TLS)

### Install

```bash
git clone https://github.com/badrust0/wutheringveil.git
cd wutheringveil/deploy

# Creates .env with strong random secrets + WV_PRODUCTION=1
./setup-prod.sh
```

Edit `.env` if you use HTTPS — set `WV_DOMAIN` and `WV_ACME_EMAIL`.

**HTTP (LAN or testing):**

```bash
docker compose up -d --build
```

**HTTPS (Let's Encrypt via Caddy):**

```bash
docker compose -f docker-compose.yml -f docker-compose.tls.yml up -d --build
```

Open the dashboard at `http://YOUR_SERVER_IP/` or `https://YOUR_DOMAIN/`. Log in with `WV_ADMIN_USER` / `WV_ADMIN_PASSWORD` from `.env`.

Verify:

```bash
curl http://localhost/api/v1/health
```

### Install agents on monitored Linux hosts

On **each machine** you want to watch:

```bash
git clone https://github.com/badrust0/wutheringveil.git   # or copy the repo
cd wutheringveil/deploy

# Use https:// if you deployed with the TLS overlay
WV_SERVER_URL=http://YOUR_SERVER_IP \
WV_API_KEY=<same as WV_AGENT_KEY in deploy/.env> \
sudo bash install-agent.sh
```

The install script installs build tools, compiles `wv-agent`, and registers a systemd service.

Check the agent:

```bash
journalctl -u wv-agent -f
```

Hosts should appear in the dashboard within a few seconds.

---

## Option B — Desktop app (local admin)

**From Releases (recommended):** install the `.deb` or AppImage — see [release/public/DESKTOP.md](release/public/DESKTOP.md).

**From source (developers):** embedded server + native window on one Linux workstation.

### Requirements

- Debian/Ubuntu-style Linux (WebKitGTK)
- **Rust** (stable, via [rustup](https://rustup.rs))
- **Node.js 20+** and npm
- **C++ toolchain:** `build-essential`, `cmake`, `libssl-dev`
- **Tauri system libs:** WebKitGTK, GTK3, etc.

### Install system packages (Debian/Ubuntu)

```bash
sudo apt-get update
sudo apt-get install -y build-essential cmake libssl-dev \
  libwebkit2gtk-4.1-dev libgtk-3-dev libayatana-appindicator3-dev librsvg2-dev
```

Install Rust and Node if missing:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# Node 20+: use your distro package or https://nodejs.org
```

### Run

**Terminal 1 — desktop app** (builds UI, starts embedded server on port **18080**):

```bash
cd wutheringveil/desktop
npm install
npm run tauri:dev
```

**Terminal 2 — agent** on the same machine:

```bash
cd wutheringveil/agent
./build.sh
./build/wv-agent --server http://127.0.0.1:18080 --key dev-agent-key
```

Login: **`admin`** / **`wv`** (defaults until changed in **Settings**).

Data: `~/.local/share/wuthering-veil/wv.db`  
Credentials: `~/.local/share/wuthering-veil/secrets.env` (random agent key created on first launch)

### Production desktop installer

```bash
cd desktop
npm install
npm run tauri build
# Output: desktop/src-tauri/target/release/bundle/ (.deb, .AppImage, etc.)
```

---

## Option C — Standalone server + browser (development)

Three terminals on a Linux (or macOS) dev machine:

**Terminal 1 — API server** (port **8080**):

```bash
cd wutheringveil/server
cargo run
```

**Terminal 2 — web UI** (port **5173**):

```bash
cd wutheringveil/web
npm install
npm run dev
```

Open **http://localhost:5173**. Login: **`admin`** / **`wv`**.

**Terminal 3 — agent** (on a Linux host, or the same box):

```bash
cd wutheringveil/agent
./build.sh
./build/wv-agent --server http://127.0.0.1:8080 --key dev-agent-key
```

---

## Agent-only install (no git on target host)

Copy `agent/` to the monitored machine, or clone once and run:

```bash
cd wutheringveil/agent
./build.sh
sudo install -m 755 build/wv-agent /usr/local/bin/wv-agent

WV_SERVER_URL=https://your-server.example.com \
WV_API_KEY=your-agent-key \
/usr/local/bin/wv-agent
```

Or use `deploy/install-agent.sh` for systemd setup (see Option A).

---

## Default credentials (development only)

| Setting | Default | Desktop (release) |
|---------|---------|-------------------|
| Dashboard user | `admin` | `admin` — change in **Settings** |
| Dashboard password | `wv` | `wv` — change in **Settings** |
| Agent key | `dev-agent-key` | Random hex in `secrets.env` — view in **Settings** |

**Change these before any real deployment.** Production Docker setup uses `./setup-prod.sh` and `WV_PRODUCTION=1` to reject weak defaults. Desktop release users should set a strong password in Settings on first login.

---

## What is not in the repository

These are created locally and are listed in `.gitignore`:

- `deploy/.env` — passwords and agent keys
- `server/data/*.db` — SQLite database
- `agent/build/`, `**/target/`, `**/node_modules/`, `**/dist/` — build output

Never commit `deploy/.env`. Use `deploy/.env.example` as a template.

---

## Troubleshooting

| Problem | What to check |
|---------|----------------|
| `docker compose` fails | Docker running? Ports 80/443 free? |
| Dashboard empty | Agent running? `WV_SERVER_URL` and `WV_API_KEY` match server `.env`? |
| Agent `HTTP 401` | Wrong `WV_API_KEY` |
| Agent `HTTP 0` | Server unreachable — firewall, URL, or TLS |
| Desktop white window | Rebuild UI: `cd desktop && npm run build && npm run tauri dev`. Don't run as root. |
| Tauri build errors | Install WebKitGTK/GTK packages (see Option B) |

More detail: [`deploy/DEPLOY.md`](deploy/DEPLOY.md) · [`README.md`](README.md)
