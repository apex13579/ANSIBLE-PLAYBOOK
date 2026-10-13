# SweattLabs — Common Ansible Role

## Purpose

The `common` role provides baseline system configuration for supported Debian and Ubuntu hosts managed by Ansible.

It centralizes common configuration tasks so other roles and playbooks can reuse consistent host-management behavior.

## Responsibilities

* Validate the target operating system.
* Configure the hostname when explicitly defined.
* Configure the system timezone.
* Refresh the APT package cache.
* Install approved baseline packages.
* Optionally enable and start the OpenSSH server.
* Provide an SSH service restart handler for tasks that explicitly notify it.

## Directory Structure

```text
roles/common/
├── defaults/
│   └── main.yml
├── handlers/
│   └── main.yml
├── tasks/
│   └── main.yml
└── README.md
```

## Supported Operating Systems

* Debian
* Ubuntu

This role is not intended for direct application to the Proxmox VE host, Unraid, or OPNsense. Those platforms require platform-specific management.

## Default Configuration

Defaults are defined in `defaults/main.yml`.

| Variable                      | Default                 | Purpose                                                |
| ----------------------------- | ----------------------- | ------------------------------------------------------ |
| `host_timezone`               | `America/New_York`      | System timezone                                        |
| `host_manage_ssh_server`      | `false`                 | Controls whether the SSH server is enabled and started |
| `common_base_packages`        | See `defaults/main.yml` | Baseline APT packages                                  |
| `common_apt_cache_valid_time` | `3600`                  | APT cache validity in seconds                          |

The `host_hostname` variable is optional and should be explicitly assigned in inventory when hostname management is required.

## Requirements

* Ansible installed on the control node.
* SSH connectivity to the target, unless using another explicitly configured connection method.
* Python 3 available on the target.
* Privilege escalation for tasks requiring administrative access.
* The `community.general` collection for the timezone module.

Install the required collection:

```bash
ansible-galaxy collection install community.general
```

## Example Variables

Define host-specific values in the appropriate inventory host or group variables.

```yaml
host_hostname: media01
host_timezone: America/New_York
host_manage_ssh_server: false
```

Use the actual hostname assigned to each machine. Do not assign the same hostname to multiple hosts.

## Example Playbook Usage

```yaml
---
- name: Apply common configuration
  hosts: linux_bootstrap
  become: true
  roles:
    - common
```

The `linux_bootstrap` group must exist in `inventory/hosts.yml` and contain only compatible hosts.

## SSH Handler

The handler in `handlers/main.yml` restarts the SSH service when explicitly notified by a task.

Example notification:

```yaml
- name: Update SSH configuration
  ansible.builtin.template:
    src: sshd_config.j2
    dest: /etc/ssh/sshd_config
    owner: root
    group: root
    mode: "0600"
    validate: "/usr/sbin/sshd -t -f %s"
  notify: Restart SSH service
```

This example requires the corresponding template to exist. Do not add or enable SSH configuration management until the template and access policy have been reviewed. Validate SSH configuration before restarting the service to reduce the risk of losing remote access.

## Safety and Scope

* The role does not configure VLANs, network interfaces, or firewall rules.
* The role does not install Docker or deploy applications.
* The role does not manage secrets.
* SSH server management is opt-in.
* Hostname management is opt-in.
* Package installation changes the target system and requires administrative privileges.

## Validation

From `lab/ansible/`, validate the project inventory and playbook syntax before applying the role:

```bash
ansible-inventory --graph
ansible-playbook --syntax-check playbooks/02-host-configuration.yml
```

Run against a specific test host before applying changes broadly.

## Maintenance

Keep shared baseline behavior in this role. Place platform-specific, application-specific, and security-monitoring configuration in their existing dedicated roles.

Update this README whenever role behavior, supported operating systems, defaults, or dependencies change.

