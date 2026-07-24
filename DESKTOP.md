# Wuthering Veil Desktop — Linux install

Single-machine SOC dashboard with embedded server. Use for **local admin** or small setups; fleet operators typically run the standalone server stack.

## Download

From [GitHub Releases](https://github.com/badrust0/wutheringveil/releases) (v1.0.1+):

| File | Install |
|------|---------|
| `wuthering-veil-desktop-VERSION-linux-amd64.AppImage` | Portable — no install |
| `wuthering-veil-desktop-VERSION-linux-amd64.deb` | Debian / Ubuntu package |

Verify: `sha256sum -c checksums.sha256`

## AppImage

```bash
chmod +x wuthering-veil-desktop-*.AppImage
./wuthering-veil-desktop-*.AppImage
```

## Debian / Ubuntu (.deb)

```bash
sudo dpkg -i wuthering-veil-desktop-*.deb
sudo apt-get install -f
# Launch from app menu: "Wuthering Veil"
```

## First run

| Item | Default |
|------|---------|
| Login | `admin` / `wv` |
| Embedded API | `http://127.0.0.1:18080` |
| Database | `~/.local/share/wuthering-veil/wv.db` |
| Credentials | `~/.local/share/wuthering-veil/secrets.env` |

First launch creates `secrets.env` with a random **agent API key**. Dashboard login defaults to `admin` / `wv` until changed.

## Settings (credentials)

Sidebar → **Settings**:

- Change **dashboard username** and **password**
- View, edit, or **regenerate** the **agent API key**
- Current password required to save
- **Restart the app** after saving (button provided)

Manual edit: `~/.local/share/wuthering-veil/secrets.env` then restart.

Changing the agent key disconnects existing agents until you update `WV_API_KEY` on each host.

## Monitor this machine

Install the release agent tarball pointing at the embedded server. Use the key from **Settings** or the Command Center empty-state panel:

```bash
tar xzf wv-agent-*-linux-amd64.tar.gz
cd wv-agent-*-linux-amd64

WV_SERVER_URL=http://127.0.0.1:18080 \
WV_API_KEY=<from Settings> \
  sudo ./install.sh
```

## Monitor remote hosts

Agents can target the desktop IP if your network allows (embedded server binds localhost only by default in v1.0.1 — remote ingest requires routing or a standalone server for production fleets). Copy the agent key from Settings and use:

```bash
WV_SERVER_URL=http://DESKTOP_LAN_IP:18080 \
WV_API_KEY=<from Settings> \
  sudo ./install.sh
```

For many hosts and a public dashboard URL, use the **server + web** Docker deploy instead of desktop.

## Requirements

- Linux x86_64 (amd64)
- WebKit/GTK runtime (bundled in AppImage; `.deb` pulls dependencies)

## Troubleshooting

| Issue | Fix |
|-------|-----|
| Blank window | `WEBKIT_DISABLE_DMABUF_RENDERER=1 ./AppImage` |
| Agent 401 | Key mismatch — copy key from Settings |
| Login fails after password change | Restart app |

See also [INSTALL.md](../../INSTALL.md) (repo root) for full public install guide.
