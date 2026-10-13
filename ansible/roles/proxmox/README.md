# Proxmox Ansible Role

**Project:** SweattLabs
**Path:** `lab/ansible/roles/proxmox/`
**Status:** Preliminary scaffold — host verification and API integration require further implementation and testing.

## Purpose

The Proxmox role provides a centralized location for Proxmox VE host validation and future API-based infrastructure management.

The SweattLabs Proxmox VE host runs on Dell PowerEdge R610 #1. It is responsible for hosting virtual machines and LXC containers for internal services, public-facing services, monitoring, security, and game servers.

## Responsibilities

* Perform preliminary operating-system checks.
* Validate required Proxmox API variables when API management is enabled.
* Provide a foundation for future Proxmox-specific automation.
* Keep hypervisor management separate from guest operating-system configuration.

## Directory Structure

```text
roles/proxmox/
├── defaults/
│   └── main.yml
├── handlers/
│   └── main.yml
└── tasks/
    └── main.yml
```

## Requirements

* Ansible connectivity to the Proxmox VE host for host-level tasks.
* Appropriate privileges for any host-level operations.
* A verified Proxmox VE installation.
* A supported Proxmox API integration for API-managed operations.
* API credentials stored securely using Ansible Vault.

## Default Variables

Defined in `defaults/main.yml`:

| Variable                        | Default | Purpose                                      |
| ------------------------------- | ------- | -------------------------------------------- |
| `proxmox_validate_host`         | `true`  | Enables preliminary host checks              |
| `proxmox_allow_unverified_host` | `false` | Controls the preliminary verification bypass |
| `proxmox_api_enabled`           | `false` | Enables API-variable validation              |

The following API variables must be supplied when API management is enabled:

* `proxmox_api_host`
* `proxmox_api_user`
* `proxmox_api_token_id`
* `proxmox_api_token_secret`

The token secret should be encrypted with Ansible Vault. Do not commit plaintext credentials to GitHub.

## Usage

The role can be included in a play targeting the verified Proxmox host:

```yaml
- name: Validate Proxmox infrastructure
  hosts: proxmox
  become: true
  roles:
    - proxmox
```

The `proxmox` inventory group must refer to the actual Proxmox VE host. Review the inventory and connection method before executing host-level tasks.

## Handlers

The role currently defines a handler named `Restart Proxmox management service`, which restarts `pvedaemon`.

This handler should only be notified by a task with a documented requirement to restart that service. It is not a generic mechanism for applying all Proxmox configuration changes.

## Architecture and Scope

The Proxmox role manages the hypervisor and its virtualization resources. It should not be used as a substitute for the `common` role on Debian or Ubuntu guests, nor should generic Linux configuration tasks be applied blindly to the hypervisor.

The intended SweattLabs architecture includes:

* **Docker VMs** for grouped internal application workloads.
* **Hardened LXC containers** for public-facing services that are not protected behind a VPN or Tailscale.
* A dedicated **Home Assistant OS VM**.
* A dedicated **Proxmox Backup Server VM**.

A PBS VM on the same physical Proxmox host does not provide independent disaster recovery against failure or loss of that host. Backup copies should also be maintained on independent storage or another system.

## Security Considerations

* Use a dedicated Proxmox automation account with least-privilege permissions.
* Store API token secrets in Ansible Vault.
* Validate TLS certificates when connecting to the Proxmox API; do not disable certificate validation as a routine workaround.
* Avoid destructive guest, storage, and network operations until explicit safeguards and validation checks exist.
* Keep public-facing services isolated from internal workloads according to the network and firewall design.

## Current Limitations

This role is **not production-ready**.

The current tasks perform preliminary checks and validate API variable presence. They do not:

* Reliably prove that the target is a Proxmox VE host.
* Test API connectivity or authenticate to the API.
* Create, modify, or delete VMs and LXC containers.
* Configure storage, bridges, VLANs, clustering, or backups.
* Validate guest health or service availability.

The `ansible_virtualization_type` fact is not a reliable universal detector for a Proxmox installation, particularly on bare-metal hypervisors. Implement a verified host-identification method before treating the preliminary assertion as a security boundary.

## Validation

Before running the role:

1. Confirm the inventory entry identifies the correct Proxmox VE host.
2. Verify Ansible connectivity and the chosen connection method.
3. Keep API management disabled until credentials and API integration are configured.
4. Run syntax checks and test against the intended host before expanding automation.

Example syntax check:

```bash
ansible-playbook playbooks/04-proxmox.yml --syntax-check
```

A syntax check verifies playbook parsing; it does not prove that the host checks, API integration, or infrastructure operations work correctly.

## Maintenance

Update this README as real Proxmox API tasks, verified host checks, and tested infrastructure-management operations are added. Document required permissions and operational risks for every task that changes guests, storage, or networking.

