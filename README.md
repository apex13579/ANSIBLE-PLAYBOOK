# Ansible Homelab Automation Plan

## Goal

Maintain the homelab's desired state — Proxmox hosts, Unraid, service VMs,
Docker workloads, monitoring, and supporting services — as reproducible
Ansible playbooks stored in GitHub. The Cisco switch and OPNsense firewall
are configured manually per their own plans and are documented here as
external dependencies.

## Scope

| In scope (Ansible) | Out of scope (manual) |
|---|---|
| Proxmox VE hosts | Cisco switch configuration |
| Unraid server | OPNsense firewall rules |
| Docker hosts and containers | Physical cabling |
| Monitoring stack | VLAN trunk design |
| Internal and public services | Hardware upgrades |
| Media, AI, and game server VMs | BIOS / firmware updates |

## Repository Layout

```
lab/ansible/
├── ansible.cfg
├── requirements.yml
├── site.yml
├── README.md
├── .gitignore
├── inventory/
│   ├── hosts.yml
│   └── group_vars/
│       ├── all.yml
│       ├── all/vault.yml
│       ├── proxmox.yml
│       ├── unraid.yml
│       ├── ai.yml
│       ├── media.yml
│       └── firewall.yml
├── host_vars/
├── playbooks/
│   ├── 01-bootstrap.yml
│   ├── 02-host-configuration.yml
│   ├── 03-network-validation.yml
│   ├── 04-proxmox.yml
│   ├── 05-core-services.yml
│   ├── 06-security-monitoring.yml
│   ├── 07-internal-applications.yml
│   ├── 08-public-services.yml
│   ├── 09-media.yml
│   ├── 10-ai.yml
│   ├── 11-game-servers.yml
│   └── 12-validation.yml
└── roles/
    ├── common/
    ├── docker/
    ├── proxmox/
    ├── unraid/
    ├── monitoring/
    ├── security/
    ├── media/
    ├── ai/
    └── firewall/
```

## Build Progress

Track overall progress at a glance. Detailed checklists are in each task
section below.

| Area | Status |
|---|---|
| Repository skeleton | In progress |
| README and documentation | In progress |
| Collections and requirements | Not started |
| Inventory and group vars | Not started |
| Vault and secrets | Not started |
| Roles | Not started |
| Playbooks | Not started |
| Validation | Not started |

---

# Task 1 — Repository Skeleton

## Goal

Establish the directory structure that all later tasks depend on.

## Structure

See the Repository Layout section above.

## Checklist

- [x] Create `lab/ansible/` directory.
- [x] Create `ansible.cfg`.
- [x] Create `inventory/` with `hosts.yml` and `group_vars/`.
- [x] Create `playbooks/` with numbered playbook files.
- [x] Create `roles/` with initial role directories.
- [ ] Add `.gitignore`.
- [ ] Add `requirements.yml`.
- [ ] Add `site.yml`.
- [ ] Add `host_vars/` directory.

---

# Task 2 — Top-Level Documentation

## Goal

Document the purpose, layout, and usage of the repo so it can be
understood by anyone (including future-me) without reading every file.

## Files

| File | Purpose |
|---|---|
| `README.md` | This document — plan, checklist, and reference |
| `roles/*/README.md` | Per-role documentation |
| `docs/` (optional) | Additional design notes if needed |

## Checklist

- [ ] README describes scope and out-of-scope items.
- [ ] README lists required Ansible and Python versions.
- [ ] README documents how to run playbooks.
- [ ] README documents where secrets live.
- [ ] Each role has a README describing its purpose and variables.
- [ ] Playbook order table matches the actual playbook files.

---

# Task 3 — Collections and Requirements

## Goal

Pin the Ansible collections and roles the repo depends on so builds are
reproducible across machines and over time.

## Files

| File | Purpose |
|---|---|
| `requirements.yml` | Declares collections and roles with versions |
| `ansible.cfg` | Configures collection paths and defaults |

## Checklist

- [ ] Create `requirements.yml`.
- [ ] Pin collection versions (e.g. `community.general: ">=8.0.0,<9.0.0"`).
- [ ] Include `ansibleguy.opnsense` if firewall automation is in scope.
- [ ] Include `community.proxmox` for Proxmox automation.
- [ ] Include `community.docker` for container management.
- [ ] Configure `ansible.cfg` with `collections_path` and defaults.
- [ ] Verify with `ansible-galaxy collection install -r requirements.yml`.

---

# Task 4 — Inventory and Group Variables

## Goal

Define which hosts Ansible manages and what variables apply to each group.

## Files

| File | Purpose |
|---|---|
| `inventory/hosts.yml` | Host and group definitions |
| `inventory/group_vars/all.yml` | Variables that apply to every host |
| `inventory/group_vars/proxmox.yml` | Proxmox-specific variables |
| `inventory/group_vars/unraid.yml` | Unraid-specific variables |
| `inventory/group_vars/ai.yml` | AI workload variables |
| `inventory/group_vars/media.yml` | Media service variables |
| `inventory/group_vars/firewall.yml` | OPNsense-related variables |
| `host_vars/<host>.yml` | Per-host overrides (IPs, storage paths) |

## Checklist

- [ ] Define all managed hosts in `hosts.yml`.
- [ ] Group hosts by function (proxmox, unraid, ai, media, firewall).
- [ ] Populate `all.yml` with common values (DNS, NTP, timezone).
- [ ] Populate each functional group var file.
- [ ] Document which variables belong in group_vars vs host_vars.
- [ ] Verify inventory with `ansible-inventory --list`.

---

# Task 5 — Vault and Secrets Management

## Goal

Keep all secrets encrypted at rest and out of GitHub.

## Files

| File | Purpose |
|---|---|
| `inventory/group_vars/all/vault.yml` | Encrypted secrets |
| `ansible.cfg` | References `ANSIBLE_VAULT_PASSWORD_FILE` |
| `.gitignore` | Excludes Vault password and transient files |

## Checklist

- [ ] Create `inventory/group_vars/all/vault.yml`.
- [ ] Encrypt with `ansible-vault encrypt`.
- [ ] Create a Vault password file **outside** the repo.
- [ ] Reference the password file in `ansible.cfg` or environment.
- [ ] Add `vault_pass*`, `*.retry`, `.ansible/` to `.gitignore`.
- [ ] Verify no plaintext secrets exist in tracked files.
- [ ] Document in README where secrets live.

---

# Task 6 — Roles

## Goal

Implement each role with tasks, handlers, defaults, and templates.

## Roles

| Role | Purpose |
|---|---|
| `common` | Base packages, users, SSH, timezone |
| `docker` | Docker Engine and Compose |
| `proxmox` | Proxmox VE configuration and templates |
| `unraid` | Unraid-specific configuration |
| `monitoring` | Prometheus, Grafana, exporters |
| `security` | Hardening, fail2ban, auditd |
| `media` | Jellyfin, *arr stack, related services |
| `ai` | AI workloads (Ollama, models, GPU config) |
| `firewall` | OPNsense automation (if in scope) |

## Checklist

- [ ] `roles/common/tasks/main.yml` implemented.
- [ ] `roles/docker/tasks/main.yml` implemented.
- [ ] `roles/proxmox/tasks/main.yml` implemented.
- [ ] `roles/unraid/tasks/main.yml` implemented.
- [ ] `roles/monitoring/tasks/main.yml` implemented.
- [ ] `roles/security/tasks/main.yml` implemented.
- [ ] `roles/media/tasks/main.yml` implemented.
- [ ] `roles/ai/tasks/main.yml` implemented.
- [ ] Each role has a populated `README.md`.
- [ ] Each role is idempotent (re-running produces no changes).

---

# Task 7 — Playbooks

## Goal

Wire roles together into ordered playbooks that build the lab from a
clean baseline.

## Playbook Order

| # | Playbook | Roles Used | Purpose |
|---|---|---|---|
| 01 | bootstrap | common | Initial access, users, SSH keys |
| 02 | host-configuration | common | Timezone, hostname, sysctl, mounts |
| 03 | network-validation | — | Verify VLAN, gateway, DNS reachability |
| 04 | proxmox | proxmox, common | Proxmox VE configuration |
| 05 | core-services | docker, monitoring | DNS, DHCP, reverse proxy, metrics |
| 06 | security-monitoring | security, monitoring | Hardening and monitoring stack |
| 07 | internal-applications | docker | Internal-only services |
| 08 | public-services | docker | Public-facing services |
| 09 | media | media, docker | Jellyfin and *arr stack |
| 10 | ai | ai, docker | AI workloads |
| 11 | game-servers | docker | Game servers |
| 12 | validation | — | Final health checks and report |

## Checklist

- [ ] `01-bootstrap.yml` implemented.
- [ ] `02-host-configuration.yml` implemented.
- [ ] `03-network-validation.yml` implemented.
- [ ] `04-proxmox.yml` implemented.
- [ ] `05-core-services.yml` implemented.
- [ ] `06-security-monitoring.yml` implemented.
- [ ] `07-internal-applications.yml` implemented.
- [ ] `08-public-services.yml` implemented.
- [ ] `09-media.yml` implemented.
- [ ] `10-ai.yml` implemented.
- [ ] `11-game-servers.yml` implemented.
- [ ] `12-validation.yml` implemented.
- [ ] `site.yml` imports all playbooks in order.

---

# Task 8 — Validation and Idempotency

## Goal

Confirm that a full run from a clean baseline works, and that a second
run produces no changes.

## Procedure

1. Provision a test host or snapshot a managed host.
2. Run `ansible-playbook site.yml`.
3. Record the first-run output.
4. Run `ansible-playbook site.yml` again.
5. Confirm the second run reports no changes to tasks that should be
   idempotent.

## Checklist

- [ ] Full run completes without errors.
- [ ] Second run is idempotent (no unexpected changes).
- [ ] Validation playbook confirms service availability.
- [ ] Network reachability confirmed from each VLAN.
- [ ] Firewall rules verified from a representative client.
- [ ] Results documented in lab notes.

---

# Task 9 — Documentation and GitHub

## Goal

Keep the repo clean, documented, and secure on GitHub.

## Checklist

- [ ] `.gitignore` excludes Vault password, `.retry`, `.ansible/`, caches.
- [ ] No secrets present in git history.
- [ ] Commit history is readable (logical commits, clear messages).
- [ ] README reflects the current state of the repo.
- [ ] Each role has a README.
- [ ] Related plans (switch, OPNsense) linked from README.
- [ ] Backup of Ansible config is part of the broader lab backup plan.

---

# Final Completion Checklist

- [ ] Repository skeleton complete.
- [ ] Documentation complete.
- [ ] Collections pinned in `requirements.yml`.
- [ ] Inventory and group vars populated.
- [ ] Vault and secrets configured.
- [ ] All roles implemented and idempotent.
- [ ] All playbooks implemented.
- [ ] `site.yml` runs the full sequence.
- [ ] Full run succeeds from a clean baseline.
- [ ] Second run is idempotent.
- [ ] Validation passes.
- [ ] Repo committed to GitHub with no secrets.

**Implementation note:** Exact collection names, module names, and role
variables depend on the Ansible version and target OS. Confirm those
details against current collection documentation before applying
changes to production hosts.
