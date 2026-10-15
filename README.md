# Ansible Homelab Automation Plan

## Goal

Maintain the homelab's desired state as reproducible Ansible playbooks
stored in GitHub. This includes the Cisco switch, the OPNsense firewall,
Proxmox hosts, Unraid, service VMs, Docker workloads, monitoring, and
supporting services. Everything that can be automated, should be.

## Scope

| In scope (Ansible) | Out of scope (manual) |
|---|---|
| Cisco switch configuration | Physical cabling |
| OPNsense firewall rules | Hardware upgrades |
| Proxmox VE hosts | BIOS / firmware updates |
| Unraid server | Initial console access setup |
| Docker hosts and containers | |
| Monitoring stack | |
| Internal and public services | |
| Media, AI, and game server VMs | |

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
│       ├── all.vault.yml
│       ├── proxmox.yml
│       ├── unraid.yml
│       ├── ai.yml
│       ├── media.yml
│       ├── firewall.yml
│       └── switch.yml
├── host_vars/
├── playbooks/
│   ├── 01-bootstrap.yml
│   ├── 02-host-configuration.yml
│   ├── 03-network-validation.yml
│   ├── 04-switch.yml
│   ├── 05-firewall.yml
│   ├── 06-proxmox.yml
│   ├── 07-core-services.yml
│   ├── 08-security-monitoring.yml
│   ├── 09-internal-applications.yml
│   ├── 10-public-services.yml
│   ├── 11-media.yml
│   ├── 12-ai.yml
│   ├── 13-game-servers.yml
│   └── 14-validation.yml
└── roles/
    ├── common/
    ├── docker/
    ├── switch/
    ├── firewall/
    ├── proxmox/
    ├── unraid/
    ├── monitoring/
    ├── security/
    ├── media/
    └── ai/
```

## Build Progress

| Area                         | Status      |
|------------------------------|-------------|
| Repository skeleton          | Done        |
| README and documentation     | In progress |
| Collections and requirements | Not started |
| Inventory and group vars     | Not started |
| Vault and secrets            | Not started |
| Switch role                  | Not started |
| Firewall role                | Not started |
| Server roles                 | Not started |
| Playbooks                    | Not started |
| Validation                   | Not started |

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
- [X] Add `.gitignore`.
- [X] Add `requirements.yml`.
- [X] Add `site.yml`.
- [X] Add `host_vars/` directory.

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

Pin the Ansible collections the repo depends on so builds are
reproducible across machines and over time.

## Required Collections

| Collection | Version | Purpose |
|---|---|---|
| `cisco.ios` | 5.x or newer | Cisco Catalyst switch configuration [citation:1] |
| `ansibleguy.opnsense` | latest | OPNsense firewall automation [citation:12] |
| `community.proxmox` | latest | Proxmox VE automation |
| `community.docker` | latest | Docker container management |
| `community.general` | latest | General-purpose modules |

**Note:** `cisco.asa` is only needed if you keep the ASA running Cisco software before the OPNsense conversion. Once converted, the ASA is just hardware running OPNsense, and the `cisco.asa` collection is no longer relevant.

## Files

| File | Purpose |
|---|---|
| `requirements.yml` | Declares collections with versions |
| `ansible.cfg` | Configures collection paths and defaults |

## Checklist

- [ ] Create `requirements.yml`.
- [ ] Pin `cisco.ios` to a known-good version.
- [ ] Pin `ansibleguy.opnsense` to a known-good version.
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
| `inventory/group_vars/switch.yml` | Cisco switch connection and VLAN vars |
| `inventory/group_vars/firewall.yml` | OPNsense API and VLAN vars |
| `inventory/group_vars/proxmox.yml` | Proxmox-specific variables |
| `inventory/group_vars/unraid.yml` | Unraid-specific variables |
| `inventory/group_vars/ai.yml` | AI workload variables |
| `inventory/group_vars/media.yml` | Media service variables |
| `host_vars/<host>.yml` | Per-host overrides (IPs, storage paths) |

## VLAN Plan (shared across switch and firewall)

| VLAN ID | Name | Subnet | Gateway |
|---:|---|---|---|
| 10 | MGMT | `10.10.10.0/24` | `10.10.10.1` |
| 15 | IOT | `10.10.15.0/24` | `10.10.15.1` |
| 20 | SERVICES | `10.10.20.0/24` | `10.10.20.1` |
| 25 | STORAGE | `10.10.25.0/24` | `10.10.25.1` |
| 30 | SECURITY | `10.10.30.0/24` | `10.10.30.1` |
| 35 | AI | `10.10.35.0/24` | `10.10.35.1` |
| 40 | MEDIA | `10.10.40.0/24` | `10.10.40.1` |
| 45 | GUEST | `10.10.45.0/24` | `10.10.45.1` |
| 50 | PUBLIC | `10.10.50.0/24` | `10.10.50.1` |
| 90 | TARPIT | `10.10.90.0/24` | `10.10.90.1` |
| 99 | IDRAC | `10.10.99.0/24` | `10.10.99.1` |

**Important:** This table must match the switch plan and OPNsense plan exactly. If it drifts, the automation will produce inconsistent state across devices.

## Checklist

- [ ] Define the switch in `hosts.yml` under a `switch` group.
- [ ] Define the OPNsense firewall in `hosts.yml` under a `firewall` group.
- [ ] Define Proxmox, Unraid, AI, and media hosts.
- [ ] Populate `all.yml` with common values (DNS, NTP, timezone).
- [ ] Populate `switch.yml` with VLAN definitions and interface names.
- [ ] Populate `firewall.yml` with VLAN interfaces and gateway addresses.
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

## Secrets Inventory

| Secret | Used By |
|---|---|
| Switch enable password | `switch` role |
| Switch admin username/password | `switch` role |
| OPNsense API key | `firewall` role |
| OPNsense API secret | `firewall` role |
| Proxmox API token | `proxmox` role |
| Docker registry credentials | `docker` role |
| Ansible Vault password | (itself) |

## Checklist

- [ ] Create `inventory/group_vars/all/vault.yml`.
- [ ] Encrypt with `ansible-vault encrypt`.
- [ ] Create a Vault password file **outside** the repo.
- [ ] Reference the password file in `ansible.cfg` or environment.
- [ ] Add `vault_pass*`, `*.retry`, `.ansible/` to `.gitignore`.
- [ ] Verify no plaintext secrets exist in tracked files.
- [ ] Document in README where secrets live.

---

# Task 6 — Switch Role

## Goal

Configure the Cisco Catalyst switch from Ansible using the `cisco.ios`
collection. This replaces the manual console work described in the
switch plan, or serves as ongoing compliance enforcement.

## Modules

| Module | Purpose |
|---|---|
| `cisco.ios.ios_vlans` | Create and manage VLANs [citation:1] |
| `cisco.ios.ios_l2_interfaces` | Configure access and trunk ports [citation:1] |
| `cisco.ios.ios_interfaces` | Configure interface attributes [citation:1] |
| `cisco.ios.ios_config` | Apply raw configuration sections [citation:1] |
| `cisco.ios.ios_facts` | Gather switch facts [citation:1] |
| `cisco.ios.ios_command` | Run show commands [citation:1] |

## Connection

The switch uses the `network_cli` connection type over SSH. Requires:

- SSH enabled on the switch
- A privilege 15 user account
- `ansible_network_os: ios` set in inventory

## Example Task

```yaml
- name: Configure VLANs
  cisco.ios.ios_vlans:
    config:
      - vlan_id: 10
        name: MGMT
      - vlan_id: 20
        name: SERVICES
    state: merged
```

## Checklist

- [ ] Create `roles/switch/tasks/main.yml`.
- [ ] Configure VLANs via `ios_vlans`.
- [ ] Configure access ports via `ios_l2_interfaces`.
- [ ] Configure trunk ports and allowed VLANs.
- [ ] Configure hostname and management IP.
- [ ] Configure SSH and local users.
- [ ] Test idempotency (second run produces no changes).
- [ ] Test against a lab switch or snapshot before production.

---

# Task 7 — Firewall Role

## Goal

Configure OPNsense firewall rules, VLAN interfaces, and gateway
addresses from Ansible using the `ansibleguy.opnsense` collection.
This replaces manual WebGUI configuration.

## Modules

| Module | Purpose |
|---|---|
| `ansibleguy.opnsense.vlan` | Create VLAN interfaces [citation:5] |
| `ansibleguy.opnsense.rule` | Create firewall rules [citation:5] |
| `ansibleguy.opnsense.interface` | Configure interface IPs |
| `ansibleguy.opnsense.reload` | Reload firewall config |

## Connection

OPNsense uses the REST API, not SSH. Requires:

- An API key and secret pair from OPNsense
- The API key stored in Ansible Vault
- `connection: local` on the play (modules run on the controller)

## Example Task

```yaml
- name: Create Management VLAN
  ansibleguy.opnsense.vlan:
    vlan_id: 10
    interface: em0
    description: "Management VLAN"
    state: present

- name: Allow MGMT to WAN
  ansibleguy.opnsense.rule:
    interface: vlan10
    source: "vlan10 net"
    destination: "any"
    action: pass
    description: "MGMT to WAN"
    state: present
```

## Checklist

- [ ] Create `roles/firewall/tasks/main.yml`.
- [ ] Configure VLAN interfaces matching the VLAN Plan.
- [ ] Configure gateway IPs on each VLAN.
- [ ] Create baseline firewall rules (MGMT allow, GUEST block, TARPIT isolate).
- [ ] Test idempotency.
- [ ] Verify rules via WebGUI after playbook run.

---

# Task 8 — Server Roles

## Goal

Implement the non-network roles for Proxmox, Unraid, Docker, and
services.

## Roles

| Role | Purpose |
|---|---|
| `common` | Base packages, users, SSH, timezone |
| `docker` | Docker Engine and Compose |
| `proxmox` | Proxmox VE configuration and templates |
| `unraid` | Unraid-specific configuration |
| `monitoring` | Prometheus, Grafana, exporters |
| `security` | Hardening, fail2ban, auditd |
| `media` | Jellyfin, *arr stack |
| `ai` | AI workloads (Ollama, GPU config) |

## Checklist

- [ ] `roles/common/tasks/main.yml` implemented.
- [ ] `roles/docker/tasks/main.yml` implemented.
- [ ] `roles/proxmox/tasks/main.yml` implemented.
- [ ] `roles/unraid/tasks/main.yml` implemented.
- [ ] `roles/monitoring/tasks/main.yml` implemented.
- [ ] `roles/security/tasks/main.yml` implemented.
- [ ] `roles/media/tasks/main.yml` implemented.
- [ ] `roles/ai/tasks/main.yml` implemented.
- [ ] Each role is idempotent.

---

# Task 9 — Playbooks

## Goal

Wire roles together into ordered playbooks that build the lab from a
clean baseline.

## Playbook Order

| # | Playbook | Target | Purpose |
|---|---|---|---|
| 01 | bootstrap | servers | Initial users, SSH keys |
| 02 | host-configuration | servers | Timezone, hostname, sysctl |
| 03 | network-validation | servers | Verify VLAN, gateway, DNS reachability |
| 04 | switch | switch | VLANs, interfaces, trunking, SSH |
| 05 | firewall | firewall | VLAN interfaces, firewall rules |
| 06 | proxmox | proxmox | Proxmox VE configuration |
| 07 | core-services | services | DNS, DHCP, reverse proxy |
| 08 | security-monitoring | all | Hardening and monitoring |
| 09 | internal-applications | services | Internal-only services |
| 10 | public-services | services | Public-facing services |
| 11 | media | media | Jellyfin and *arr stack |
| 12 | ai | ai | AI workloads |
| 13 | game-servers | services | Game servers |
| 14 | validation | all | Final health checks |

## Checklist

- [ ] `01-bootstrap.yml` implemented.
- [ ] `02-host-configuration.yml` implemented.
- [ ] `03-network-validation.yml` implemented.
- [ ] `04-switch.yml` implemented.
- [ ] `05-firewall.yml` implemented.
- [ ] `06-proxmox.yml` implemented.
- [ ] `07-core-services.yml` implemented.
- [ ] `08-security-monitoring.yml` implemented.
- [ ] `09-internal-applications.yml` implemented.
- [ ] `10-public-services.yml` implemented.
- [ ] `11-media.yml` implemented.
- [ ] `12-ai.yml` implemented.
- [ ] `13-game-servers.yml` implemented.
- [ ] `14-validation.yml` implemented.
- [ ] `site.yml` imports all playbooks in order.

---

# Task 10 — Validation and Idempotency

## Goal

Confirm that a full run from a clean baseline works, and that a second
run produces no changes.

## Procedure

1. Snapshot the switch and OPNsense configs (or ensure backups exist).
2. Run `ansible-playbook site.yml`.
3. Record the first-run output.
4. Run `ansible-playbook site.yml` again.
5. Confirm the second run reports no changes.

## Checklist

- [ ] Full run completes without errors.
- [ ] Second run is idempotent on switch tasks.
- [ ] Second run is idempotent on firewall tasks.
- [ ] Second run is idempotent on server tasks.
- [ ] Validation playbook confirms service availability.
- [ ] Network reachability confirmed from each VLAN.
- [ ] Firewall rules verified from a representative client.
- [ ] Results documented in lab notes.

---

# Task 11 — Documentation and GitHub

## Goal

Keep the repo clean, documented, and secure on GitHub.

## Checklist

- [ ] `.gitignore` excludes Vault password, `.retry`, `.ansible/`, caches.
- [ ] No secrets present in git history.
- [ ] Commit history is readable.
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
- [ ] Switch role implemented and idempotent.
- [ ] Firewall role implemented and idempotent.
- [ ] Server roles implemented and idempotent.
- [ ] All playbooks implemented.
- [ ] `site.yml` runs the full sequence.
- [ ] Full run succeeds from a clean baseline.
- [ ] Second run is idempotent.
- [ ] Validation passes.
- [ ] Repo committed to GitHub with no secrets.

**Implementation note:** Cisco IOS module syntax, OPNsense module
parameters, and collection versions change over time. Confirm against
current documentation before applying to production devices. The switch
plan and OPNsense plan remain the authoritative source for the intended
network design; this repo implements that design.
