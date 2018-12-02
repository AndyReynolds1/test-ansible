# test-ansible
Testing configuring Ansible for Windows hosts

## Setup Environment
Use Vangrant to setup local Ubuntu and Windows VMs in VirtualBox.

## Configure Ansible Controller
Install required components:
```
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install software-properties-common python3-pip -y
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt-get install ansible -y
sudo pip install pywinrm
```

Confirm Ansible working: `ansible --version`

Create folder structure:
```
mkdir test
cd test/
```

Create inventory file: `nano inventory.yml`
```
---
[web]
192.168.125.3
```

Create Ansible config file: `nano ansible.cfg`
```
[defaults]
inventory = ~/test/inventory.yml
```

Create group variables file: `mkdir group_vars && nano group_vars/web.yml`
```
ansible_user: vagrant
ansible_password: vagrant
ansible_connection: winrm
ansible_winrm_transport: ntlm
ansible_port: 5985
```

Test connection to Windows host: `ansible web -i inventory.yml -m win_ping`

Create playbook: `nano iis.yml`
```
---
- hosts: web
  tasks:
  - name: Ensure IIS web server is installed
    win_feature:
	  name: Web-Server
	  state: present
```

Run playbook: `ansible-playbook iis.yml`
