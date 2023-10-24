# Host preparation to install k3s

Author: <https://github.com/x3nomorpheus>

## Playbook for preparing host

This playbooks prepare hosts to installing k3s. It adds local user (or users) with sudo privileges, updating system and running ssh_hardening.

## System requirements
Playbooks tested on Ansible [core 2.14.2]
with python version = 3.11.2 and jinja version = 3.1.2 
It supports two distro: Ubuntu and Rocky Linux for now.

## Usage

First of all install collections from galaxy with 
```bash
ansible-galaxy collection install -r collections/requirements.yml
```

After that set proper hosts for you in `inventory/hosts.ini`.

If you want you can set some variables by your needs in **host_preparation.yaml**. List of all avaliable variables you can find in `roles/ssh_hardening/defaults/main.yaml`

And we are ready to go with

```bash 
ansible-playbook host_preparation.yaml
``` 
