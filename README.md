# Initial deployment of k3s

Author: <https://github.com/x3nomorpheus>

## Preinstall

This playbook prepare hosts to installing k3s. It adds local user (or users) with sudo privileges, and ssh key for user, updating system and running ssh_hardening. Also, for Rocky it disables firewalld and for Ubuntu it opens new ssh port on ufw.

## Install

This role was copied from official repo of ansible k3s deployment. Also, for reduce unused roles the raspberry role was deleted. The other things are the same. If you want more info about you can find it [here](https://github.com/k3s-io/k3s-ansible)

## Ingress

Just setting up nginx ingress controller.

## Postinstall

With this role we install pip, kubernetes library, create working directory for some custom values of helm charts and install helm. Next it fully deploys certmanager and creates issuer for future certs.  After that it deploys wordpress with helm (which inludes enabled apache-exporter and ingress object).

## Monitoring

Monitoring role installs kube-prometheus-stack which includes full prometheus stack, grafana and also prometheus operator. Installation of kube-prometheus-stack includes ingress resources for alertmanager and grafana, two imported dashboards in grafana for mysql and apache exporter data visualisation. After that it creates deployment of mysql exporter, creates user for it *(with privileges PROCESS, REPLICATION CLIENT, REPLICATION SLAVE)*, service for it and also creates serviceMonitors for apache and mysql exporter to add targets into the prometheus. Following step it adds rules for apache and mysql. And at final task it installs efk stack.

### ElasticSearch Fluentd Kibana deployment

This playbook is a part of monitoring role. It installs python deps for using htpasswd which creates attrs for kibana. The feature of this playbook is that all deployments were written in manifests without using helm and special operators. 
> [!NOTE]
> There is a bug in fluentd timestamp parser, so the solution for this was using the variable efk_timezone which must be setted in type like `"+03:00"`, but for most servers the value of this variable must be *Z*

## System requirements
Playbooks tested on Ansible [core 2.14.2]
with python version = 3.11.2 and jinja version = 3.1.2 
It supports two distro: Ubuntu and Rocky Linux for now.

## Usage

### Main steps

First of all install collections from galaxy with 
```bash
ansible-galaxy collection install -r collections/requirements.yml
```

After that set proper hosts for you in `inventory/hosts.ini`.

If you want you can set some variables by your needs in `inventory/group_vars/all.yml`. List of all avaliable variables you can find in `roles/*/defaults/main.yaml`

*Create file `inventory/vault-pass.yaml` for set next values:*

```
grafana_password: "<set_your_password_here>"
kibana_username: "<set_your_password_here>"
kibana_password: "<set_your_password_here>"

```
> [!IMPORTANT]
>For the testing purposes set certmanager_issuer: letsencrypt-staging in `inventory/group_vars/all.yml`


And we are ready to go with

```bash 
ansible-playbook init.yaml
``` 

### Running specific roles

In some cases you may want to run only specific step and ignore others. For these purposes tags was implemented, and you feel free to use it by your needs.

There are 4 tags:
* preinstall
* install
* postinstall
* monitoring

All this tags match headings above.

For using tag run:

```bash
ansible-playbook init.yaml --tag install
```

### Reset k3s

As mentioned above for installing k3s was used official repo of k3s ansible installation, so this role was brought from there too. From its name we can understand that this will delete installation of k3s.

To reset k3s run:

```bash
ansible-playbook reset.yaml
```
