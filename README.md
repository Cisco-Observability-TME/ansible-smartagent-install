# Ansible Playbook for Deploying Cisco AppDynamics Smart Agent

This repository contains an Ansible playbook for deploying the Cisco AppDynamics Smart Agent on multiple hosts. The playbook supports installation on both Debian and RedHat-based systems.

## Prerequisites

- Ansible installed on the control machine.
- SSH access to target hosts.
- Smart Agent installation packages available in the playbook directory.

## Files

### Inventory File

Define your hosts and authentication details in the `inventory.yaml` file.

```yaml
all:
  hosts:
    smartagent-hosts:
      ansible_host: 54.173.1.106
      ansible_username: ec2-user
      ansible_password: ins3965!
      ansible_become: yes
      ansible_become_method: sudo
      ansible_become_password: ins3965!
      ansible_ssh_common_args: '-o StrictHostKeyChecking=no'

    smartagent-hosts:
      ansible_host: 192.168.86.107
      ansible_username: aleccham
      ansible_password: ins3965!
      ansible_become: yes
      ansible_become_method: sudo
      ansible_become_password: ins3965!

    smartagent-host-1:
      ansible_host: 54.82.95.69
      ansible_username: ubuntu
      ansible_password: ins3965!
      ansible_become: yes
      ansible_become_method: sudo
      ansible_become_password: ins3965!
    smartagent-host-2:
      ansible_host: <IP_ADDRESS_2>
      ansible_username: <USERNAME_2>
      ansible_password: <PASSWORD_2>
      ansible_become: yes
      ansible_become_method: sudo
      ansible_become_password: <BECOME_PASSWORD_2>
    smartagent-host-3:
      ansible_host: <IP_ADDRESS_3>
      ansible_username: <USERNAME_3>
      ansible_password: <PASSWORD_3>
      ansible_become: yes
      ansible_become_method: sudo
      ansible_become_password: <BECOME_PASSWORD_3>
