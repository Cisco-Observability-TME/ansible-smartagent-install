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
```

### Variables File

Define the Smart Agent configuration details in the `vars.yml` file.

```yaml
smart_agent:
  controller_url: 'CONTROLLER URL HERE, JUST THE BASE URL'
  account_name: 'Account Name Here'
  account_access_key: 'YOUR ACCESS KEY HERE'
  fm_service_port: '443' # Use 443 or 8080 depending on your environment.
  ssl: true

smart_agent_package_debian: 'appdsmartagent_64_linux_24.6.0.2143.deb'  # or the appropriate package name
smart_agent_package_redhat: 'appdsmartagent_64_linux_24.6.0.2143.rpm'  # or the appropriate package name
```

## Usage

1. Clone the repository:
    ```sh
    git clone https://github.com/your-repo/appdynamics-smart-agent-deploy.git
    cd appdynamics-smart-agent-deploy
    ```

2. Update the `inventory.yaml` and `vars.yml` files with your configuration.

3. Run the playbook:
    ```sh
    ansible-playbook -i inventory.yaml playbook.yml
    ```


Adjust the details in the `inventory.yaml` and `vars.yml` files to match your specific environment before running the playbook.
