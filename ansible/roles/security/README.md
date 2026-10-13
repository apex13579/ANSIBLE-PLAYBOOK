# Security Ansible Role

**Project:** SweattLabs
**Path:** `lab/ansible/roles/security/`
**Status:** Initial scaffold — security controls are not yet implemented.

## Purpose

Provide a controlled foundation for security validation and future security automation across the SweattLabs homelab.

## Responsibilities

* Validate security-management opt-in.
* Validate requested security-control identifiers.
* Reject unsupported control names.
* Report selected controls without changing the target host.

## Directory Structure

```text
roles/security/
├── defaults/
│   └── main.yml
├── handlers/
│   └── main.yml
├── tasks/
│   └── main.yml
└── README.md
```

## Default Variables

| Variable                      | Default                  | Purpose                          |
| ----------------------------- | ------------------------ | -------------------------------- |
| `security_enabled`            | `false`                  | Enables validation tasks         |
| `security_supported_controls` | Five control identifiers | Allowlist of recognized controls |
| `security_controls`           | `[]`                     | Controls selected for validation |

Example configuration:

```yaml
security_enabled: true
security_controls:
  - ssh_audit
  - firewall_audit
  - access_review
```

This example enables validation only. It does not implement or execute those security controls.

## Intended Controls

* **SSH audit:** Review SSH configuration and remote-access exposure.
* **Firewall audit:** Validate firewall policy and intended network boundaries.
* **Access review:** Review privileged accounts and authorized access.
* **Security updates:** Assess update status and approved patching requirements.
* **Audit logging:** Validate logging configuration and retention requirements.

## Platform Boundaries

Security tasks must account for the actual operating system and management method:

* Debian/Ubuntu hosts can use appropriate Linux modules and supported system tools.
* Proxmox VE requires hypervisor-aware procedures.
* Unraid requires compatible Unraid management procedures.
* OPNsense is FreeBSD-based and must not receive generic Linux hardening tasks.
* Docker containers and public-facing LXC containers require controls appropriate to their deployment boundaries.

Do not assume a single hardening task set is safe for every host.

## Security Requirements

* Store secrets in Ansible Vault or an approved secrets manager.
* Preserve SSH access and validate configuration before applying changes.
* Avoid locking out management access through untested firewall or SSH changes.
* Use least-privilege accounts and narrowly scoped permissions.
* Test potentially disruptive controls before broad deployment.

## Current Limitations

This role is a validation scaffold. It does not currently audit SSH, inspect firewall rules, patch systems, change user privileges, or configure audit logging.

## Validation

Run a syntax check before execution:

```bash
ansible-playbook playbooks/06-security-monitoring.yml --syntax-check
```

A syntax check confirms parsing only. It does not prove that security controls work or that the playbook targets the intended hosts.

## Maintenance

Implement and test each control separately. Document its supported platforms, required privileges, operational impact, and recovery procedure before marking it complete.

