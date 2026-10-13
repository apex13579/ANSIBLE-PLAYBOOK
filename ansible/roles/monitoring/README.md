# Monitoring Ansible Role

**Project:** SweattLabs
**Path:** `lab/ansible/roles/monitoring/`
**Status:** Initial scaffold — monitoring deployment is not yet implemented.

## Purpose

The monitoring role provides a centralized foundation for validating and eventually deploying the SweattLabs monitoring stack.

The intended stack includes Prometheus for metrics collection, Grafana for visualization, Loki for log aggregation, Uptime Kuma for service availability monitoring, and Scrutiny for storage-device health monitoring.

Internal monitoring applications are intended to run in Docker on their designated hosts.

## Responsibilities

* Validate whether monitoring management is enabled.
* Validate the configured monitoring application list.
* Reject application names outside the supported allowlist.
* Report the selected applications without changing the target.
* Provide a foundation for future monitoring deployment tasks.

## Directory Structure

```text
roles/monitoring/
├── defaults/
│   └── main.yml
├── handlers/
│   └── main.yml
├── tasks/
│   └── main.yml
└── README.md
```

## Requirements

* A valid Ansible inventory entry for the target host.
* Ansible connectivity to the selected host.
* Appropriate privileges for future installation and configuration tasks.
* A verified Docker deployment environment for internal containerized monitoring services.
* Application-specific configuration, persistent storage, networking, and secrets defined before deployment.

## Default Variables

Defined in `defaults/main.yml`:

| Variable                            | Default                                          | Purpose                              |
| ----------------------------------- | ------------------------------------------------ | ------------------------------------ |
| `monitoring_enabled`                | `false`                                          | Enables the role's validation path   |
| `monitoring_supported_applications` | Prometheus, Grafana, Loki, Uptime Kuma, Scrutiny | Application allowlist                |
| `monitoring_applications`           | `[]`                                             | Applications selected for deployment |

Example configuration:

```yaml
monitoring_enabled: true

monitoring_applications:
  - prometheus
  - grafana
  - loki
```

This example selects applications for validation only. It does not deploy them.

## Supported Applications

| Application | Intended purpose                           |
| ----------- | ------------------------------------------ |
| Prometheus  | Metrics collection and time-series storage |
| Grafana     | Metrics and log visualization              |
| Loki        | Log aggregation and querying               |
| Uptime Kuma | Service availability monitoring            |
| Scrutiny    | Storage-device health monitoring           |

These entries form an allowlist for validation. They do not establish that an application has been installed, configured, or tested.

## Usage

The role can be included in a play targeting the confirmed monitoring deployment host:

```yaml
- name: Validate monitoring configuration
  hosts: monitoring_hosts
  roles:
    - monitoring
```

`monitoring_hosts` is an illustrative inventory group, not a confirmed group in the current project. Reconcile the playbook target with the actual inventory before execution.

## Handlers

`handlers/main.yml` is currently a placeholder. No handlers are defined.

Future handlers should be added only when a task changes a service configuration and a restart or reload is required. Handler actions must match the actual deployment method and service names.

## Architecture

The intended monitoring stack includes:

* Prometheus for metrics.
* Grafana for dashboards.
* Loki for logs.
* Uptime Kuma for availability checks.
* Scrutiny for storage health.

Before deployment, define how monitoring components will reach their targets, persist their data, authenticate users, and communicate across VLANs. Apply firewall rules that permit only the required monitoring traffic.

Do not expose internal monitoring interfaces directly to the public internet. If remote access is required, use the established access-control and public-service architecture.

## Security Considerations

* Store credentials and tokens in Ansible Vault or another approved secrets manager.
* Restrict access to dashboards and administrative interfaces.
* Define retention limits for metrics and logs.
* Avoid collecting unnecessary sensitive data in logs.
* Restrict scrape targets and monitoring endpoints to approved hosts and services.
* Protect monitoring configuration and persistent data with appropriate backup procedures.

## Current Limitations

This role is a **validation scaffold, not a deployment implementation**.

It currently does not:

* Install Docker or Docker Compose.
* Deploy containers or compose projects.
* Configure Prometheus scrape targets.
* Provision Grafana data sources or dashboards.
* Configure Loki ingestion and retention.
* Configure Uptime Kuma monitors.
* Configure Scrutiny collectors or storage access.
* Set up alerting, notifications, backups, or service health checks.

## Validation

Before implementing deployment tasks:

1. Confirm the monitoring host and inventory group.
2. Select the intended applications.
3. Define persistent data locations and network requirements.
4. Implement deployment tasks and application-specific configuration.
5. Test service health, data persistence, authentication, and recovery.

Example syntax check:

```bash
ansible-playbook playbooks/06-security-monitoring.yml --syntax-check
```

A syntax check verifies parsing only; it does not test monitoring deployment or functionality.

## Maintenance

Keep this documentation synchronized with the role defaults, tasks, handlers, and deployment architecture. Mark individual capabilities complete only after they have been implemented and tested.

