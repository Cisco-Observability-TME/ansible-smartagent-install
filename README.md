Here is the GitHub README in markdown format:

```markdown
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

### Playbook

The `playbook.yml` file contains tasks to install and configure the Smart Agent.

```yaml
---
- name: Deploy Cisco AppDynamics Distribution of OpenTelemetry Collector
  hosts: all
  become: yes
  vars_files:
    - variables.yaml

  tasks:
    - name: Ensure required packages are installed (RedHat)
      yum:
        name:
          - yum-utils
        state: present
        update_cache: yes
      when: ansible_os_family == "RedHat"

    - name: Ensure required packages are installed (Debian)
      apt:
        name:
          - curl
          - debian-archive-keyring
          - apt-transport-https
          - software-properties-common
        state: present
        update_cache: yes
      when: ansible_os_family == "Debian"

    - name: Ensure the directory exists
      file:
        path: /opt/appdynamics/appdsmartagent
        state: directory
        mode: '0755'

    - name: Check if config.ini exists
      stat:
        path: /opt/appdynamics/appdsmartagent/config.ini
      register: stat_config

    - name: Create default config.ini file if it doesn't exist
      copy:
        dest: /opt/appdynamics/appdsmartagent/config.ini
        mode: '0644'
        content: |
          [default]
          AccountAccessKey="{{ smart_agent.account_access_key }}"
          ControllerURL="{{ smart_agent.controller_url }}"
          ControllerPort=443
          AccountName="{{ smart_agent.account_name }}"
          FMServicePort={{ smart_agent.fm_service_port }}
          EnableSSL={{ smart_agent.ssl | ternary('true', 'false') }}
      when: not stat_config.stat.exists

    - name: Configure Smart Agent
      lineinfile:
        path: /opt/appdynamics/appdsmartagent/config.ini
        regexp: '^{{ item.key }}='
        line: "{{ item.key }}={{ item.value }}"
      loop:
        - { key: 'AccountAccessKey', value: "{{ smart_agent.account_access_key }}" }
        - { key: 'ControllerURL', value: "{{ smart_agent.controller_url }}" }
        - { key: 'AccountName', value: "{{ smart_agent.account_name }}" }
        - { key: 'FMServicePort', value: "{{ smart_agent.fm_service_port }}" }
        - { key: 'EnableSSL', value: "{{ smart_agent.ssl | ternary('true', 'false') }}" }

    - name: Set the Smart Agent package path (Debian)
      set_fact:
        smart_agent_package: "{{ playbook_dir }}/appdsmartagent_64_linux_24.6.0.2143.deb"
      when: ansible_os_family == "Debian"

    - name: Set the Smart Agent package path (RedHat)
      set_fact:
        smart_agent_package: "{{ playbook_dir }}/appdsmartagent_64_linux_24.6.0.2143.rpm"
      when: ansible_os_family == "RedHat"

    - name: Fail if Smart Agent package not found (Debian)
      fail:
        msg: "Smart Agent package not found for Debian."
      when: ansible_os_family == "Debian" and not (smart_agent_package is defined and smart_agent_package is file)

    - name: Fail if Smart Agent package not found (RedHat)
      fail:
        msg: "Smart Agent package not found for RedHat."
      when: ansible_os_family == "RedHat" and not (smart_agent_package is defined and smart_agent_package is file)

    - name: Copy Smart Agent package to target (Debian)
      copy:
        src: "{{ smart_agent_package }}"
        dest: "/tmp/{{ smart_agent_package | basename }}"
      when: ansible_os_family == "Debian"

    - name: Install Smart Agent package (Debian)
      command: dpkg -i /tmp/{{ smart_agent_package | basename }}
      when: ansible_os_family == "Debian"

    - name: Copy Smart Agent package to target (RedHat)
      copy:
        src: "{{ smart_agent_package }}"
        dest: "/tmp/{{ smart_agent_package | basename }}"
      when: ansible_os_family == "RedHat"

    - name: Install Smart Agent package (RedHat)
      yum:
        name: "/tmp/{{ smart_agent_package | basename }}"
        state: present
        disable_gpg_check: yes
      when: ansible_os_family == "RedHat"

    - name: Restart Smart Agent service
      service:
        name: smartagent
        state: restarted

    - name: Clean up temporary files
      file:
        path: "/tmp/{{ smart_agent_package | basename }}"
        state: absent
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

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
```

Adjust the details in the `inventory.yaml` and `vars.yml` files to match your specific environment before running the playbook.
