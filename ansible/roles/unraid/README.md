# Unraid Ansible Role

**Project:** SweattLabs
**Path:** `lab/ansible/roles/unraid/`
**Status:** Initial scaffold — management integration not yet implemented.

## Purpose

The Unraid role provides a controlled foundation for managing the SweattLabs storage server running on Dell PowerEdge R610 #2.

Unraid is responsible for primary NAS storage and its managed application containers. This role is intended to support future Unraid-specific automation without treating the host as a standard Debian or Ubuntu server.

## Responsibilities

* Validate required Unraid inventory variables.
* Require explicit opt-in before the role proceeds.
* Provide defaults for future container and storage management.
* Establish a location for future Unraid-specific handlers.
* Preserve Unraid's native management boundaries.

## Directory Structure

```text
roles/unraid/
├── defaults/
│   └── main.yml
├── handlers/
│   └── main.yml
├── tasks/
│   └── main.yml
└── README.md
```

## Requirements

* Ansible connectivity to the Unraid host.
* A verified hostname and inventory entry.
* A confirmed management method compatible with the installed Unraid version.
* Appropriate authentication and permissions for any future management operations.

The current role does not yet verify the Unraid operating system or connect to an Unraid API.

## Default Variables

Defined in `defaults/main.yml`:

| Variable                   | Default   | Purpose                                        |
| -------------------------- | --------- | ---------------------------------------------- |
| `unraid_manage_enabled`    | `false`   | Enables the current role's validation path     |
| `unraid_manage_containers` | `false`   | Reserved for future container-management tasks |
| `unraid_manage_storage`    | `false`   | Reserved for future storage-management tasks   |
| `unraid_hostname`          | Undefined | Expected hostname, to be defined in inventory  |

The management flags are safeguards, not implemented features. Enabling a flag does not itself deploy containers or change storage configuration.

## Usage

The role can eventually be included in a play targeting the verified Unraid inventory group:

```yaml
- name: Validate Unraid management configuration
  hosts: unraid
  roles:
    - unraid
```

The `unraid` group must exist in the inventory. Review the connection method and host variables before execution.

The current task file requires `unraid_manage_enabled: true` and a nonempty `unraid_hostname`. Otherwise, its assertions fail intentionally.

## Container Management

The following applications are planned for the Unraid host:

* Vaultwarden
* Nextcloud
* Immich
* Paperless-ngx
* SearXNG
* code-server
* Scrypted
* Frigate

These applications should be managed through Unraid's supported container-management environment. This role must not install a separate Docker Engine on the host.

Future automation should define how container configuration, persistent application data, secrets, updates, and rollback are handled before making changes.

## Storage Management

Unraid storage changes can affect array availability and data integrity. Future tasks must distinguish routine share management from destructive operations involving disks, pools, parity, or filesystem configuration.

Storage-management tasks should remain disabled until their supported interface, safeguards, and recovery procedures have been verified.

## Handlers

`handlers/main.yml` is currently a placeholder. No handlers are defined, and no services or containers are restarted.

Add handlers only when a real task requires an action supported by the verified Unraid management method.

## Security Considerations

* Do not store passwords, API tokens, or private keys in plaintext in Git.
* Use Ansible Vault for applicable secrets.
* Restrict management access to the appropriate management network.
* Avoid destructive array, pool, parity, and filesystem operations without a tested recovery plan.
* Do not assume that Debian/Ubuntu package or service-management tasks are compatible with Unraid.

## Current Limitations

This role is a **scaffold, not a production-ready Unraid automation implementation**.

It currently performs basic variable assertions and displays a validation message. It does not:

* Reliably identify the running Unraid operating system.
* Authenticate to an Unraid API.
* Manage Docker containers.
* Configure shares or storage.
* Change array, pool, parity, or filesystem settings.
* Verify application health or backup integrity.

## Validation

Before implementing management tasks:

1. Confirm the Unraid inventory entry and hostname.
2. Verify the supported connection and authentication method.
3. Test read-only operations first.
4. Add container and storage automation separately, with appropriate safeguards.
5. Test changes against a recoverable environment before applying them to the primary NAS.

## Maintenance

Update this README whenever management capabilities are implemented. Document supported Unraid versions, required permissions, configuration variables, and recovery procedures. Do not mark a capability complete until it has been tested on the intended host.

