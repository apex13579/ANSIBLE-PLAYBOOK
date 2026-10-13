# GIT-DB-1-05-ANSIBLE-PLAYLIST


lab/ansible/
├── ansible.cfg
├── inventory/
│   ├── hosts.yml
│   └── group_vars/
│       ├── all.yml
│       ├── proxmox.yml
│       ├── unraid.yml
│       ├── ai.yml
│       └── media.yml
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
    └── ai/
