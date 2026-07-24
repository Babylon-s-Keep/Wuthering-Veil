# Wuthering Veil v1.0.1

**Downloads:** [GitHub Releases](https://github.com/badrust0/wutheringveil/releases)

| File | Purpose |
|------|---------|
| `wuthering-veil-desktop-1.0.1-linux-amd64.AppImage` | Portable desktop app |
| `wuthering-veil-desktop-1.0.1-linux-amd64.deb` | Debian / Ubuntu package |
| `wv-agent-1.0.1-linux-amd64.tar.gz` | Linux host agent |
| `checksums.sha256` | Verify all artifacts |

```bash
sha256sum -c checksums.sha256
```

## Highlights

- **Desktop app** now on Releases (AppImage + `.deb`)
- **Settings** — change username, password, and agent API key in-app
- Random agent key created on first launch (`~/.local/share/wuthering-veil/secrets.env`)
- Agent tarball rebuilt for 1.0.1

## Quick install

**Desktop:** `chmod +x wuthering-veil-desktop-*.AppImage && ./wuthering-veil-desktop-*.AppImage`

**Agent:** extract tarball, then `WV_SERVER_URL=… WV_API_KEY=… sudo ./install.sh`

Full guide: [INSTALL.md](INSTALL.md)

## After upgrading from 1.0.0

- Desktop users: sign in, open **Settings**, set a strong password, note your agent key, restart if prompted
- Agents: no change required unless you rotated the agent key in Settings

See [CHANGELOG.md](CHANGELOG.md) for full history.
