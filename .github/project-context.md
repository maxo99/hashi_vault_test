# Project Context for hashi_vault_test Repository

## Overview

This repository is for learning and implementing Infrastructure as Code (IaC) with Terraform/OpenTofu, Ansible, and Proxmox. The goal is to create a fully automated Proxmox homelab setup with proper user management and SSH key automation using HashiCorp Vault.

## Infrastructure Setup

### Current Environment

- **Proxmox VE**: Newly deployed instance
- **HashiCorp Vault**: Running in Docker container on dedicated host (version 1.20.1)
- **Development Machine**: Laptop running Linux with Ansible in Python virtual environment
- **SSH Keys**: Stored in `~/.ssh/homelab/` directory structure for laptop-based development

### Network Configuration

Network details, DNS servers, domain, and timezone are configured in `ansible/inventory/group_vars/all.yml`. Host-specific configurations are in `ansible/inventory/hosts.ini` and respective group_vars directories.

## Current Project State

### Completed Tasks

- ✅ HashiCorp Vault deployment via Docker and Ansible automation
- ✅ Vault initialization, unsealing, and post-install configuration
- ✅ Vault KV v2 engines enabled at `ansible/` and `terraform/` paths
- ✅ Vault AppRole authentication configured for CI/CD workflows
- ✅ Ansible virtual environment setup with required dependencies
- ✅ Basic Proxmox post-install configuration (repositories, packages, DNS, fail2ban)
- ✅ SSH key generation for terraform-prov user
- ✅ Vault secrets storage and retrieval integration
- ✅ SSH user creation (terraform-prov) on Proxmox host
- ✅ SSH key deployment to terraform-prov user

### In Progress

- 🔄 Proxmox API group and user creation
- 🔄 API token generation for automation

### Key Files and Configurations

#### Ansible Configuration

- **Virtual Environment**: `ansible/.venv/` with required dependencies
- **Inventory**: `ansible/inventory/hosts.ini`
- **Environment Variables**: `ansible/.env` (contains VAULT_TOKEN and VAULT_ADDR)
- **Main Playbooks**:
  - `ansible/vault_deploy.yml` - Vault deployment and container setup
  - `ansible/vault_post_install.yml` - Vault configuration and policy setup
  - `ansible/pve_setup.yml` - Proxmox post-install configuration
- **Global Variables**: `ansible/inventory/group_vars/all.yml`
- **Vault Variables**: `ansible/inventory/group_vars/vault.yml`
- **Host-specific Variables**: `ansible/inventory/group_vars/pve_01/vars.yml`

#### Vault Integration

- **KV Engines**: `ansible/` and `terraform/` paths in Vault
- **Secrets Path**: `ansible/data/proxmox`
- **Stored Secrets**: Admin credentials for initial Proxmox access
- **Authentication**: Root token accessed via environment variable
- **AppRole**: Configured for CI/CD automation (future use)
- **Deployment**: Docker container with persistent storage and init script

## Automation Strategy

### Authentication Flow

1. **Initial Setup**: Root/password authentication to Proxmox
2. **User Creation**: Ansible creates terraform-prov SSH user
3. **SSH Key Deployment**: Public key added to terraform-prov authorized_keys
4. **API Setup**: Proxmox API user/group/token creation
5. **Future Automation**: SSH key + API token authentication

### Development Approach

- **Option 1 (Current)**: Laptop-based development with local SSH keys
- **Option 2 (Future)**: Full Vault-centric SSH key management
- **Priority**: Simple, working setup before advanced features

## Commands and Workflows

### Environment Setup

```bash
cd ansible/
uv sync
source .venv/bin/activate
```

### Available Commands

The project uses a `justfile` for command automation. Run `just --list` in the `ansible/` directory to see all available commands.

## Repository Structure

The main components are organized under the `ansible/` directory with playbooks, roles, and inventory configuration. Use standard file system tools or your editor to explore the current structure.

## Known Issues and Resolutions

### Variable Inheritance

- **Issue**: SSH key variables need to be in pve-01 group_vars, not vault group
- **Solution**: Variables moved to `pve_01/vars.yml`

### Vault Authentication

- **Issue**: Token needs to be available to Ansible tasks
- **Solution**: Environment variables set via justfile or manual export

### Vault Policy Parsing

- **Issue**: Policy creation failed with HCL parsing errors
- **Solution**: Simplified policy generation using heredoc syntax and proper cleanup

## Next Steps

1. Complete Proxmox API group/user creation
2. Generate and store API tokens in Vault
3. Test full SSH key automation workflow
4. Implement VM provisioning with OpenTofu/Terraform
5. Migrate to Vault-centric SSH key management approach
6. Consider implementing CI/CD workflows using AppRole authentication
