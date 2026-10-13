# Media Ansible Role

**Project:** SweattLabs
**Path:** `lab/ansible/roles/media/`
**Status:** Initial scaffold — application deployment is not yet implemented.

## Purpose

The media role provides a foundation for validating and eventually deploying media applications on the SweattLabs media machine: an HP EliteDesk 800 G3 SFF running Debian.

Media applications are intended to run in Docker on this host. Media libraries and download data should reside on NAS storage rather than consuming the host's operating-system disk.

## Responsibilities

* Validate media-management opt-in.
* Validate the Docker project directory.
* Validate the intended media data mount configuration.
* Validate the selected application list.
* Reject unsupported application identifiers.
* Report configuration without modifying the host.

## Directory Structure

```text
roles/media/
├── defaults/
│   └── main.yml
├── handlers/
│   └── main.yml
├── tasks/
│   └── main.yml
└── README.md
```

## Default Variables

| Variable                       | Default                       | Purpose                           |
| ------------------------------ | ----------------------------- | --------------------------------- |
| `media_enabled`                | `false`                       | Enables validation tasks          |
| `media_docker_project_dir`     | `/opt/sweattlabs/media`       | Intended Docker project directory |
| `media_data_mounts`            | `{}`                          | NAS data and mountpoint mapping   |
| `media_supported_applications` | Eight application identifiers | Supported application allowlist   |
| `media_applications`           | `[]`                          | Selected applications             |

Example configuration:

```yaml
media_enabled: true

media_docker_project_dir: /opt/sweattlabs/media

media_data_mounts:
  # Replace with verified values for your NAS exports and mountpoints.
  media_library:
    source: "REPLACE_WITH_VERIFIED_NAS_EXPORT"
    path: "/mnt/media"
  downloads:
    source: "REPLACE_WITH_VERIFIED_DOWNLOAD_EXPORT"
    path: "/mnt/downloads"

media_applications:
  - jellyfin
  - sonarr
  - radarr
  - prowlarr
  - qbittorrent
```

The mount example illustrates the intended variable shape only. The current role does not mount NAS shares, and the source values must be replaced with actual exports before implementation.

## Supported Applications

* **Jellyfin:** Media streaming and library management.
* **Sonarr:** Television-series management.
* **Radarr:** Movie management.
* **Lidarr:** Music management.
* **Prowlarr:** Indexer management.
* **Bazarr:** Subtitle management.
* **qBittorrent:** Download client.
* **Usenet:** Reserved identifier for the selected Usenet client or service configuration.

The `usenet` identifier is a logical category, not a specific container image. Choose and document the actual client before deployment.

## Storage Design

The media host is planned with a 32 GB OS disk, 8 GB RAM, four CPU cores, and Intel Quick Sync capability.

The OS disk should hold Debian, Docker components, and application configuration as appropriate. Large media libraries and downloads should be stored on NAS-backed shares.

Before implementing mounts, decide and verify:

* NAS export paths and protocols.
* Host mountpoints and ownership.
* UID/GID and permissions required by each application.
* Startup ordering and recovery behavior if NAS storage is unavailable.
* Consistent paths between download clients and media-management applications.
* Backup coverage for application configuration and important metadata.

Avoid downloading or writing large media files to the OS disk accidentally when a NAS mount is missing.

## Requirements

* Debian on the media host.
* Working Ansible connectivity and privilege escalation.
* Docker Engine and Compose support installed through the designated Docker role.
* Verified NAS connectivity and mount configuration.
* Application-specific configuration, secrets, and persistent storage paths.

## Current Limitations

This role is a **validation scaffold**, not a deployment implementation. It does not:

* Install Docker.
* Mount NAS shares.
* Create Docker networks or volumes.
* Deploy or update containers.
* Configure Intel Quick Sync device access.
* Configure download clients, libraries, or application integration.
* Verify service health or backups.

## Validation

Check playbook syntax before execution:

```bash
ansible-playbook playbooks/09-media.yml --syntax-check
```

Syntax checking does not verify NAS access, container deployment, or media playback. Test those behaviors separately after implementation.

## Maintenance

Keep this README aligned with the actual variable structure and deployment tasks. Do not mark media deployment complete until storage mounts, container configuration, permissions, hardware acceleration, and application health have been tested.

```
```

