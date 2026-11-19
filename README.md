# Ansible nginx role on AWS EC2

Configures an Ubuntu EC2 web server with nginx using a reusable Ansible role, run from a separate Ansible control node.

## Architecture

```
Laptop (WSL) --SSH--> Control node (Ansible) --SSH, private IP--> Managed node (nginx)
```
- 2 x EC2 t3.micro, Ubuntu 24.04, eu-north-1 (default VPC)
- The managed node's security group only allows SSH from the control node's security group

## Project structure

```
.
├── nginx-playbook.yml      # playbook: which hosts, run as root, which role
├── inventory.example       # template inventory (real one is git-ignored)
└── roles/nginx/
    ├── tasks/main.yml      # install, start and enable nginx
    └── meta/main.yml       # role info (author, licence)
```

## How to run

```bash
cp inventory.example inventory      # then add your managed node's private IP
ansible-playbook -i inventory nginx-playbook.yml --syntax-check
ansible-playbook -i inventory nginx-playbook.yml
ansible -i inventory webservers -m shell -a "curl -s localhost"
```

## What the role does

- Updates the apt cache and installs nginx
- Starts nginx and enables it on boot
- Idempotent: a second run reports `changed=0`

## Screenshots

(added in the next step)

## Problems I solved

- **SSH between nodes:** allowed SSH on the managed node only from the control node's security group (least privilege).
- **Passphrase-protected key:** loaded it with `ssh-agent` and `ssh-add` after each login.
- **Secrets in Git:** kept the real inventory and keys out with `.gitignore`, and committed a template instead.
- **Playbook not found:** caused by a filename typo, and fixed with a clear naming convention.

## Improvements

- Handler to reload nginx when its config changes
- Custom index page with a Jinja2 template
- Ansible Vault for secrets
- ansible-lint in GitHub Actions (CI)
- Terraform to provision the EC2s

## Tech

Ansible, AWS EC2, Ubuntu, nginx, Git/GitHub
