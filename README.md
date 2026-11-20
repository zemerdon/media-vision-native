# Media Vision

**One native application. Two independent media managers: Series and Movies.**

Media Vision is a self-hosted media management application for **TV series and movies**. Its Series
and Movies managers share an interface but keep their own operational settings, providers, indexers,
download clients, queues, histories, quality profiles, root folders, monitoring and workflows.
Neither manager silently falls back to the other's configuration.

The application is released as a **native, self-contained Linux x64 service**, with an interactive
**Proxmox VE / Debian 13 LXC installer**. No Docker stack is required.

[Latest Media Vision Stable
release](https://github.com/zemerdon/media-vision-native/releases/latest) · [Media Vision
API](https://github.com/zemerdon/media-vision-api-native) · [Release
history](https://github.com/zemerdon/media-vision-native/releases)

> **Two independent products.** Media Vision Core (this repository) and Media Vision API have
> **separate installers, data, configurations, updates and backups**. You can install Core before
> configuring the API, but for a complete new setup, installing the API first is convenient. This
> repository distributes **public, versioned native binaries and installers**; application source is
> maintained separately.

## What you get

| Area | Media Vision Core |
| --- | --- |
| Series | TV library, monitoring, imports, profiles, indexers, download clients, queue and history |
| Movies | Independent movie library, monitoring, imports, profiles, indexers, download clients, queue and history |
| Interface | Web UI, responsive layouts, shared presentation settings and distinct Series/Movies sections |
| Maintenance | System status, updates, health information, and Core-only backup/restore |
| Optional integration | Connect to the separately installed Media Vision API for metadata services |
| Deployment | Debian 13 native Proxmox LXC; self-contained Linux x64 releases |

## Quick installation — both one-liners

Run these commands **as root on your Proxmox VE host** (not inside an LXC, not on a desktop). Each
installer creates a **separate new LXC** and lets you select an unused VMID.

**1. Install Media Vision API** — independent metadata service, admin UI and IMDb database
management:

```bash
bash -c "$(curl -fsSL https://github.com/zemerdon/media-vision-api-native/releases/latest/download/bootstrap-native.sh)"
```

**2. Install Media Vision Core** — the Series and Movies application:

```bash
bash -c "$(curl -fsSL https://github.com/zemerdon/media-vision-native/releases/latest/download/bootstrap-native.sh)"
```

You can install **Core alone** if you aren't ready to configure the API. Its installer does not
require an API URL or key.

**Important:** these commands are for a **new installation**, not an upgrade or replacement of an
existing LXC. Review downloaded scripts before running them if you prefer. Both native installers
check the official Stable release manifest and SHA-256 hashes of their packages before provisioning.

### Default requirements

| Setting | Media Vision Core | Media Vision API |
| --- | --- | --- |
| Guest operating system | Debian 13 LXC | Debian 13 LXC |
| CPU | 4 vCPU | 4 vCPU |
| Memory | 4,096 MiB | 4,096 MiB |
| Root disk | 16 GiB | 20 GiB |
| Network | Proxmox `vmbr0`; DHCP by default | Proxmox `vmbr0`; DHCP by default |
| Native web interface | `http://<core-ip>:9191` | `http://<api-ip>:9393` |
| Client API | Core's own API | `http://<api-ip>:9292` |
| Containers | **Two separate LXCs** | **Two separate LXCs** |

You need a working Proxmox VE host, an available LXC storage pool and Debian container-template
storage. The interactive installer uses `whiptail`; install it on the **Proxmox host** if missing.
Proxmox commands such as `pct` and `pvesh` must be available.

Choose **Default Install**, **Advanced Install**, **User Defaults** or **Settings**. Advanced mode
allows changing storage, bridge, IPv4, resources, timezone and optional root SSH settings. Root SSH
password access is **disabled by default**. Neither installer creates or modifies your host media
mounts.

## First-time setup

1. **Set up the API if installed.** Open `http://<api-ip>:9393` on your trusted network. Configure
   IMDb sources, API keys and dataset download/build as required. The API installer does **not**
   include an IMDb index or enable automatic refresh. Its authenticated metadata endpoint is on port
   **9292**.
2. **Open Media Vision.** Browse to `http://<core-ip>:9191` and complete the application setup.
3. **Connect the products.** In Media Vision, go to **MV API** and enter the independent API **admin
   URL** (`http://<api-ip>:9393`). The API metadata listener (`:9292`) is a different, key-protected
   interface.
4. **Configure media storage.** Create Proxmox bind mounts or other authorized access to your
   libraries outside the installer. Configure **Series** and **Movies** root folders and operational
   settings **separately**; verify download-client paths and permissions.
5. **Verify.** Confirm the Core UI and both managers work, then verify API connection/status and
   your provider/indexer/download-client settings.

> **Security:** The current standalone MV API administration UI on port **9393 has no password
> login**. Anyone who can reach that port can administer API keys and data. Keep it restricted to a
> trusted management network and **never expose 9393 directly to the internet**. Port 9292 uses
> separately managed API client keys.

## Updating

- **Media Vision Core:** use **System → Updates** in the application and select a verified
  **Stable** release. Review its changelog, make a backup and install from the Core updater.
- **Media Vision API:** has its **own** software version, native updater and rollback mechanism.
  Manage its status independently through its Web UI or the connected Core **System → Updates**
  component controls.
- **MVArtwork:** is a separately versioned optional component managed by the API updater; it is not
  part of a Core software release.

The one-liners above are intended for **fresh LXCs**, not in-place updates. New Stable releases
appear at [Media Vision releases](https://github.com/zemerdon/media-vision-native/releases/latest)
and [Media Vision API
releases](https://github.com/zemerdon/media-vision-api-native/releases/latest). No public binary
release contains your private settings or media.

## Backups and migration

Use **System → Backup** in Core to export a verified portable recovery ZIP. Core can include the
main database/configuration, cached **MediaCover** artwork and persistent custom application data.
This preserves the database records and separate Series/Movies operational settings but **does not**
copy actual movies, episodes, downloads, Proxmox bind mounts, OS configuration or all browser-local
UI preferences.

**Back up Media Vision API separately**: its configuration/hashed API keys and optional full IMDb
SQLite database are managed by the API's own backup workflow. A large SQLite snapshot needs
sufficient **temporary disk space on the API host**. Back up optional MVArtwork data separately
where needed.

For a clean migration, **retain the original LXCs** until externally downloaded ZIPs have been
verified and both products have been restored into fresh installations. Install/restore API first,
connect the new API administrator URL in Core, restore Core and independently check **Series and
Movies** paths, profiles, clients and queues. Restoring Core does not magically recreate source
media mounts.

## Release verification and downloads

The public [Stable release](https://github.com/zemerdon/media-vision-native/releases/latest)
provides:

- `bootstrap-native.sh` — permanent Proxmox installer entry point.
- `media-vision-app-<version>-linux-x64.tar.gz` — native Core application.
- `media-vision-native-proxmox-<version>.tar.gz` — versioned installer.
- `release-manifest.json` — product, version, channel, source commit, SHA-256 and download metadata.

The installer verifies the versioned archive against the matching manifest before creating the new
guest. Release tags and public assets are versioned; old releases are preserved for rollback. The
distribution repository does not mirror the private development source tree.

## Troubleshooting

| Symptom | Check |
| --- | --- |
| Installer says not root / missing `pct` | Run it on the **Proxmox host as root**, not in a guest |
| Visual menu cannot start | Install `whiptail` on Proxmox and use an interactive terminal |
| UI does not open on `:9191` | Confirm LXC networking, the native service and host/firewall rules |
| Media not visible | Configure/verify Proxmox media mounts and guest file permissions |
| API disconnected | Check the configured **admin URL on 9393**, API LXC status and network access |
| Metadata database empty | Initialize and build/download datasets separately in the API WebUI |
| Recovery appears incomplete | Restore and validate **both products independently** and check physical mount paths |

**Links:** [Core Stable downloads](https://github.com/zemerdon/media-vision-native/releases/latest)
· [API installation and administration](https://github.com/zemerdon/media-vision-api-native#readme)
· [All Core releases](https://github.com/zemerdon/media-vision-native/releases)

---

*Media Vision and Media Vision API are separate products. This is the public Core release
repository, not a copy of the private application source or an installer for Sonarr/Radarr.*
