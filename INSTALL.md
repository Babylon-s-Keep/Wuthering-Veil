# Installing Wuthering Veil

**Downloads:** [github.com/badrust0/wutheringveil/releases](https://github.com/badrust0/wutheringveil/releases)

Use the **named release files** on that page (`wv-agent-…`, `wuthering-veil-desktop-…`, `checksums.sha256`).  
Ignore GitHub’s auto-generated **“Source code (zip/tar.gz)”** — this repository does not publish application source.

**Current version:** see [VERSION](VERSION)

---

## What you can install today

| Component | Release file | Purpose |
|-----------|--------------|---------|
| **Desktop app** | `wuthering-veil-desktop-1.0.1-linux-amd64.AppImage` or `.deb` | Admin workstation — embedded server + native UI |
| **Linux agent** | `wv-agent-1.0.1-linux-amd64.tar.gz` | Monitor a Linux host (metrics + security signals) |
| **Server + web dashboard (Docker)** | *Not yet a public binary* | Fleet control plane — operator deploys from private source today |

Verify downloads:

```bash
sha256sum -c checksums.sha256
```

---

## 1. Desktop app (admin workstation)

Single-machine Security Operation Center: embedded Rust server, React UI in a native window, local SQLite database.

### Debian / Ubuntu

```bash
sudo dpkg -i wuthering-veil-desktop-1.0.1-linux-amd64.deb
sudo apt-get install -f    # fix missing dependencies if needed
```

Launch from the application menu, or:

```bash
wuthering-veil-desktop
```

### AppImage (portable)

```bash
chmod +x wuthering-veil-desktop-1.0.1-linux-amd64.AppImage
./wuthering-veil-desktop-1.0.1-linux-amd64.AppImage
```

### First launch

| Item | Value |
|------|--------|
| Default login | `admin` / `wv` |
| Embedded API | `http://127.0.0.1:18080` |
| Database | `~/.local/share/wuthering-veil/wv.db` |
| Credentials file | `~/.local/share/wuthering-veil/secrets.env` |

On first start the app creates `secrets.env` with a **random agent API key**. The default dashboard login is `admin` / `wv` until you change it.

### Change username, password, or agent key

1. Sign in to the desktop app  
2. Open **Settings** in the sidebar  
3. Edit dashboard login and/or agent API key  
4. Enter your **current password** and click **Save credentials**  
5. Click **Restart now** (or quit and reopen the app)

You can also edit `~/.local/share/wuthering-veil/secrets.env` by hand and restart.

**Important:** If you change the agent key, existing agents must be reconfigured with the new `WV_API_KEY`.

### Monitor remote Linux hosts from desktop

The desktop embedded server listens on localhost. Point agents at it from other machines on your network (firewall permitting):

1. In the app, open **Command Center** (empty state) or **Settings** — copy the **agent API key**  
2. On each Linux host, install the agent tarball (see below) with:

```bash
WV_SERVER_URL=http://YOUR_DESKTOP_IP:18080 \
WV_API_KEY=<key from Settings or Command Center> \
  sudo ./install.sh
```

Replace `YOUR_DESKTOP_IP` with the desktop machine’s LAN IP. The embedded server binds to `127.0.0.1` only — remote agents require network routing/NAT or a standalone server deploy for production fleets.

---

## 2. Linux agent (monitored hosts)

**File:** `wv-agent-1.0.1-linux-amd64.tar.gz`

On **each Linux host** you want in the fleet:

```bash
tar xzf wv-agent-1.0.1-linux-amd64.tar.gz
cd wv-agent-1.0.1-linux-amd64

WV_SERVER_URL=https://YOUR_WV_SERVER \
WV_API_KEY=YOUR_AGENT_KEY \
  sudo ./install.sh
```

| Variable | Description |
|----------|-------------|
| `WV_SERVER_URL` | Control plane URL — **no trailing slash** |
| `WV_API_KEY` | Must match `WV_AGENT_KEY` on the server (desktop: see Settings) |
| `WV_INTERVAL` | Optional poll interval in seconds (default `5`) |
| `WV_PLUGIN_DIR` | Optional plugin directory (default `/etc/wv/plugins`) |

**Verify:**

```bash
systemctl status wv-agent
journalctl -u wv-agent -f
```

The host should appear in the dashboard within ~30 seconds.

**Plugins (optional):** place `.so` files in `/etc/wv/plugins/` and restart the agent.

### Agent on the same machine as desktop

```bash
WV_SERVER_URL=http://127.0.0.1:18080 \
WV_API_KEY=<your key from Settings> \
  sudo ./install.sh
```

---

## 3. Standalone server + web dashboard

Pre-built Docker images for a multi-host fleet control plane are **not yet** on the public Releases page.

For production server deployment, operators use the private source tree and Docker Compose (`deploy/release-v1.sh`). Contact your administrator for:

- Dashboard URL  
- `WV_AGENT_KEY` for agent installs  

Until server images ship publicly, use the **desktop app** for local evaluation or a privately deployed server.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `ingest failed (HTTP 401)` | Wrong `WV_API_KEY` — must match server/desktop agent key |
| `ingest failed (HTTP 0)` | Server unreachable, TLS error, or firewall |
| Agent not in dashboard | Check `WV_SERVER_URL`; wait ~30s; `systemctl status wv-agent` |
| Desktop blank / white window | Try `WEBKIT_DISABLE_DMABUF_RENDERER=1` before launching |
| Login fails after changing password | Restart the app after saving Settings |
| Forgot desktop password | Stop app; edit `secrets.env`; restart (or reset `WV_ADMIN_PASSWORD` there) |

---

See [README.md](README.md) for architecture and feature overview.
