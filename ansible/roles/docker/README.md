# Docker Ansible Role

**Project:** SweattLabs
**Path:** `lab/ansible/roles/docker/`
**Status:** Initial implementation — repository setup required before first-time installation.

## Purpose

The Docker role installs and manages Docker Engine on supported Debian and Ubuntu hosts. It provides a reusable foundation for deploying internal applications through Docker across the SweattLabs homelab.

## Responsibilities

* Validate that the target host runs Debian or Ubuntu.
* Refresh the APT package cache.
* Install Docker Engine and associated CLI, container runtime, Buildx, and Compose packages.
* Ensure the Docker service is enabled and running according to configured defaults.
* Provide a handler for restarting Docker when a configuration task requires it.

## Directory Structure

```text
roles/docker/
├── defaults/
│   └── main.yml
├── handlers/
│   └── main.yml
└── tasks/
    └── main.yml
```

## Requirements

* Ansible connectivity to the target host.
* Python 3 on the managed host.
* Debian or Ubuntu.
* Privilege escalation enabled by the calling play.
* Docker's official APT repository and signing key configured before installing the Docker CE packages.

## Default Variables

Variables are defined in `defaults/main.yml` and can be overridden by inventory, group variables, host variables, or play-level variables.

| Variable                      | Default                                            | Purpose                       |
| ----------------------------- | -------------------------------------------------- | ----------------------------- |
| `docker_packages`             | Docker CE, CLI, containerd, Buildx, Compose plugin | Packages to install           |
| `docker_apt_cache_valid_time` | `3600`                                             | APT cache validity in seconds |
| `docker_service_name`         | `docker`                                           | Docker system service name    |
| `docker_service_enabled`      | `true`                                             | Enable Docker at boot         |
| `docker_service_state`        | `started`                                          | Desired Docker service state  |

## Usage

Include the role in a play targeting an approved Docker host:

```yaml
- name: Configure Docker hosts
  hosts: docker_hosts
  become: true
  roles:
    - docker
```

The `docker_hosts` inventory group is an example. Use the actual groups defined in the SweattLabs inventory before running the playbook.

## Handler

The `Restart Docker service` handler restarts Docker when a task notifies it:

```yaml
notify: Restart Docker service
```

Handlers run at the end of the play by default. Multiple notifications to the same handler are consolidated into one execution.

## Architecture and Scope

SweattLabs uses Docker for internal services, including applications hosted on Unraid and Debian-based AI and media machines, as well as internal application workloads hosted on Proxmox Docker VMs.

Public-facing services that are not protected behind a VPN or Tailscale are intended to run in isolated, hardened LXC containers instead.

This role does not deploy application containers or compose projects. Those responsibilities require separate, explicitly defined tasks and application configuration.

## Security Considerations

* Do not store credentials, API tokens, or private keys in plaintext in this role or in Git.
* Restrict Docker socket access. Membership in the `docker` group effectively grants root-level control over the host.
* Apply host firewall and network controls appropriate to each Docker host.
* Avoid exposing the Docker API over an unauthenticated network connection.
* Review container privileges, bind mounts, and published ports before deploying applications.

## Current Limitations

**This role is not yet a complete first-time Docker installation.**

Before deployment, implement and verify the official Docker APT repository and signing-key configuration for the supported distributions. The current package task assumes that prerequisite already exists.

The role also does not configure Docker daemon settings, create networks or volumes, mount NAS storage, or deploy application containers.

## Validation

After completing the repository prerequisite, validate the role before deployment:

```bash
ansible-playbook playbooks/02-host-configuration.yml --syntax-check
ansible-playbook playbooks/02-host-configuration.yml --list-hosts
```

These commands validate playbook syntax and show the selected hosts; they do not prove Docker can be installed or that the service is healthy. Perform a controlled deployment against a test host before wider use.

## Maintenance

Keep this documentation synchronized with the role's defaults, tasks, handlers, and supported host groups. Do not describe the role as production-ready until repository configuration and end-to-end installation have been tested.

