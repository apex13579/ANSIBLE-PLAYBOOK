# AI Ansible Role

**Project:** SweattLabs
**Path:** `lab/ansible/roles/ai/`
**Status:** Initial scaffold — application deployment is not yet implemented.

## Purpose

The AI role provides a foundation for validating and eventually deploying AI-related applications on the SweattLabs AI machine, an OptiPlex 3040 MT running Debian.

The planned applications are Llama for local language-model inference, GoboNet, and n8n for workflow automation. Applications are intended to run in Docker on the Debian host.

## Responsibilities

* Validate explicit opt-in for AI management.
* Validate the Docker project and data directories.
* Check the configured minimum host memory.
* Validate selected application identifiers.
* Report configuration without modifying the host.

## Directory Structure

```text
roles/ai/
├── defaults/
│   └── main.yml
├── handlers/
│   └── main.yml
├── tasks/
│   └── main.yml
└── README.md
```

## Default Variables

| Variable                    | Default              | Purpose                                       |
| --------------------------- | -------------------- | --------------------------------------------- |
| `ai_enabled`                | `false`              | Enables validation tasks                      |
| `ai_docker_project_dir`     | `/opt/sweattlabs/ai` | Intended Docker project directory             |
| `ai_data_dir`               | `/srv/ai-data`       | Intended model and application data directory |
| `ai_minimum_memory_mb`      | `8192`               | Minimum memory check                          |
| `ai_supported_applications` | Llama, GoboNet, n8n  | Supported application allowlist               |
| `ai_applications`           | `[]`                 | Applications selected for validation          |

Example configuration:

```yaml
ai_enabled: true
ai_applications:
  - llama
  - gobonet
  - n8n
```

This configuration enables validation only. It does not deploy applications or download models.

## Planned Applications

* **Llama:** Local language-model inference, planned for CPU-only execution.
* **GoboNet:** AI-related application; confirm its image, dependencies, and deployment requirements before implementation.
* **n8n:** Workflow automation, with access to the local AI API configured only when needed.

The name `llama` is a logical application identifier, not a specific container image or model version. Those choices must be explicitly configured before deployment.

## Hardware and Performance

The target system is an OptiPlex 3040 MT with Debian bare metal. The plan calls for using available CPU cores for local inference and initially allocating approximately 8–16 GB RAM and 100 GB of storage to the AI workload.

The configured 8 GB minimum is only a validation threshold. Actual model requirements depend on model architecture, parameter count, quantization, context length, concurrent requests, and other applications running on the host.

CPU-only inference may be practical for smaller quantized models but can be slow. Measure memory use, inference latency, and sustained CPU load before choosing a model for regular use.

## Storage

Keep model files and application data separate from the OS filesystem where practical. Confirm that the data directory has adequate capacity and backup coverage.

The current role does not create directories, download models, configure permissions, or mount external storage.

## Requirements

* Debian on the AI host.
* Working Ansible connectivity and appropriate privileges.
* Docker Engine and Compose support installed through the designated Docker role.
* Verified application images, versions, configuration, and persistent storage paths.
* Secure handling of API keys and other credentials.

## Security Considerations

* Do not expose the local model API publicly without a deliberate access-control design.
* Restrict n8n access and protect stored workflow credentials.
* Store secrets in Ansible Vault or an approved secrets manager.
* Restrict container privileges and mounted host paths.
* Apply network controls so AI services are reachable only by approved systems.

## Current Limitations

This role is a **validation scaffold**, not a deployment implementation. It does not:

* Install Docker.
* Deploy containers or Compose projects.
* Download or select model weights.
* Configure CPU limits, thread counts, or inference parameters.
* Configure GoboNet or n8n.
* Configure API authentication or network access.
* Verify inference performance or application health.

## Validation

Run a syntax check before execution:

```bash
ansible-playbook playbooks/10-ai.yml --syntax-check
```

Syntax checking does not verify memory suitability, model compatibility, deployment success, or inference performance.

## Maintenance

Keep this README synchronized with the defaults and actual deployment tasks. Mark deployment complete only after the selected applications, model, persistent storage, security controls, and performance have been tested on the target host.
