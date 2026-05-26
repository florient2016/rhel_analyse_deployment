# rhel_analyse_deployment

Deployment repository for the **RHEL System Analysis** playbook.  
This repo consumes the [`rhel_analyse`](https://github.com/florient2016/rhel_analyse_role) role via `requirements.yml`.

## Repository Layout

```
rhel_analyse_deployment/
├── ansible.cfg                  # Ansible configuration
├── requirements.yml             # Role dependencies (git-based)
├── site.yml                     # Main playbook
├── inventory/
│   └── hosts.ini                # Inventory (adapt to your env)
└── group_vars/
    └── rhel_servers.yml         # Variables for all RHEL hosts
```

## Quick Start

### 1. Clone this repo

```bash
git clone https://github.com/florient2016/rhel_analyse_deployment.git
cd rhel_analyse_deployment
```

### 2. Install the role from requirements.yml

```bash
ansible-galaxy install -r requirements.yml
```

> The role is pulled directly from the `rhel_analyse_role` Git repository.

### 3. Adapt the inventory

Edit `inventory/hosts.ini` and add your RHEL 9/10 hosts:

```ini
[default]
localhost
```

### 4. Run the playbook

```bash
# Analyse all servers
ansible-playbook site.yml

# Limit to a specific host or group
ansible-playbook site.yml --limit server1.example.com

# Override the output directory
ansible-playbook site.yml -e "rhel_analyse_output_dir=/var/tmp/reports"

# Don't fetch report to control node
ansible-playbook site.yml -e "rhel_analyse_fetch_report=false"
```

### 5. View the report

Reports are fetched to `./reports/` on the control node:

```bash
ls reports/
# server1.example.com_output.html
# server2.example.com_output.html

# Open in browser (Linux)
xdg-open reports/server1.example.com_output.html

# Open in browser (macOS)
open reports/server1.example.com_output.html
```

## Variables (group_vars/rhel_servers.yml)

| Variable | Default | Description |
|---|---|---|
| `rhel_analyse_output_dir` | `/tmp/rhel_analyse` | Remote report directory |
| `rhel_analyse_output_file` | `output.html` | Report filename |
| `rhel_analyse_fetch_report` | `true` | Fetch report to control node |
| `rhel_analyse_local_report_dir` | `./reports` | Local directory for fetched reports |
| `rhel_analyse_disk_warn_percent` | `80` | Disk usage warning threshold |
| `rhel_analyse_mem_warn_percent` | `80` | Memory usage warning threshold |
| `rhel_analyse_top_packages` | `30` | Number of packages to show in report |

## Requirements

- Ansible ≥ 2.14
- Python ≥ 3.9 on control node
- SSH access + `sudo` on target hosts
- RHEL / Rocky / AlmaLinux / Oracle Linux **9 or 10**

## Two-Repo Architecture

```
┌──────────────────────────────────────────┐
│  rhel_analyse_role  (Role Repository)    │
│                                          │
│  tasks/       templates/  defaults/      │
│  handlers/    vars/       meta/          │
└─────────────────────┬────────────────────┘
                      │ referenced by
                      ▼
┌──────────────────────────────────────────┐
│  rhel_analyse_deployment (This Repo)     │
│                                          │
│  requirements.yml  →  installs role      │
│  site.yml          →  calls role         │
│  inventory/        →  your hosts         │
│  group_vars/       →  role variables     │
└──────────────────────────────────────────┘
```

## License

