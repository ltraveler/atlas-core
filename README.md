# 🌍 Atlas Core

![Ansible Lint](https://github.com/ltraveler/atlas-core/actions/workflows/lint.yml/badge.svg)

**A practical, industry-standard Ansible blueprint designed to teach you how to build, automate, and manage your own home laboratory or small business infrastructure**

---

## 🎯 Purpose of This Project

Setting up self-hosted infrastructure from scratch can feel overwhelming. I created **Atlas Core** as an educational, real-world reference to demonstrate how anyone—from a homelab enthusiast to a small company, can deploy and run their own independent software stack using **Infrastructure-as-Code (IaC)**.

Instead of clicking through manual setup wizards or maintaining messy shell scripts, this repository shows how to:
* **Build from Zero:** Transform bare Linux servers into a structured, reliable environment
* **Self-Host Enterprise Tools:** Easily deploy and integrate popular services like **GitLab**, **Confluence**, and containerized applications
* **Scale Cleanly:** Add new subprojects as modular, plug-and-play Ansible roles without breaking existing services
* **Follow Production Standards:** Apply real-world DevOps practices—including secret encryption, idempotency, and automated linting—on a small-team or personal budget

---

## 🏗️ Architecture Overview

This repository follows the official Ansible role-based monorepo layout. Your control machine orchestrates one or multiple target servers, keeping configuration variables completely separated from application logic.

```mermaid
graph TD
    A[Control Node/ Your Laptop] →|SSH + Ansible| B(Target Server/VM)
    B --> C[Role: common<br/>Security, Firewall & Base Packages]
    B --> D[Role: docker<br/>Container Runtime]
    B --> E[Role: gitlab<br/>Self-Hosted Git & CI/CD]
    B --> F[Role: confluence<br/>Team Wiki & Docs]
```

---

## 📂 Repository Structure

```text
.
├── ansible.cfg              # Global Ansible behavior and inventory defaults
├── requirements.yml         # External community roles and collections
├── inventory/               # Server definitions and environment variables
│   ├── hosts.yml            # Target server IPs and groups
│   ├── group_vars/          # Shared settings and encrypted Ansible Vault secrets
│   └── host_vars/           # Server-specific overrides
├── playbooks/               # Entrypoints to deploy all or single services
│   ├── site.yml             # Master playbook for the entire infrastructure
│   ├── gitlab.yml           # Standalone GitLab deployment
│   └── confluence.yml       # Standalone Confluence deployment
└── roles/                   # Modular, reusable subprojects
    ├── bedrock-common/      # OS baseline, users, and SSH hardening
    ├── vessel-docker/       # Docker Engine and Compose setup
    ├── forge-gitlab/              # GitLab service configuration
    └── archive-confluence/          # Confluence service configuration
```

---

## 🚀 Quick Start Guide

### 1. Prerequisites
* **Control Node:** Ansible (`2.16+`) and Python (`3.10+`) installed on your local machine
* **Target Host(s):** One or more fresh Ubuntu/Debian VMs or bare-metal servers accessible via SSH

### 2. Clone & Install Dependencies
```bash
git clone https://github.com/ltraveler/atlas-core.git
cd atlas-core
ansible-galaxy install -r requirements.yml
```

### 3. Configure Your Inventory
Update `inventory/hosts.yml` with your server IP addresses and adjust your domain names and ports inside `inventory/group_vars/all/vars.yml`

### 4. Encrypt Your Secrets
Never store passwords in plain text. Create and encrypt your sensitive variables using Ansible Vault:
```bash
ansible-vault create inventory/group_vars/all/vault.yml
```

### 5. Deploy Your Infrastructure

Open `playbooks/acore-site.yml` and comment or uncomment the roles you want to include in your stack:

```yaml
- name: Provision Atlas Core Infrastructure
  hosts: all
  become: true
  roles:
    - aegis_common          # Always recommended
    - hermes_docker         # Container runtime
    - hephaestus_gitlab     # Self-hosted Git & CI/CD
    # - athena_confluence   # Uncomment to deploy Confluence
```

Once configured, run the master playbook to provision your selected services:

```bash
# Deploy the configured stack
ansible-playbook playbooks/acore-site.yml --ask-vault-pass
```

> **💡 Pro Tip:** You can also assign **Ansible tags** to each role inside `acore-site.yml` (e.g., `tags: gitlab`) so users can run a single subproject without editing the file:
> ```bash
> ansible-playbook playbooks/acore-site.yml --tags "gitlab" --ask-vault-pass
> ```

---

## 🛠️ Roadmap & Planned Subprojects
- [x] Base repository architecture & role scaffolding
- [ ] `aegis_common` role (UFW firewall, Fail2ban, essential packages)
- [ ] `hermes_docker` role (Container runtime & reverse proxy)
- [ ] `hephaestus_gitlab` role (Self-hosted Git & CI/CD runners)
- [ ] `athena_confluence` role (Knowledge base & documentation)
- [ ] Automated CI/CD pipeline with `ansible-lint`

---

## 🤝 Contributing & Learning
Whether you are building your first home server or setting up internal tools for a startup, feel free to fork this repository, open issues with questions, or submit pull requests with new service roles!

## 📄 License
This project is licensed under the **MIT License** - feel free to use and adapt it for your personal homelab or company infrastructure.
