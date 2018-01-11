# Deployment

A repository for managing deployment processes. Single-purpose successor to https://github.com/IATI/IATI-Websites

## Ansible test

Based on guide at http://ryaneschinger.com/blog/ansible-quick-start/

The file structure should follow the Best Practice Directory Layout http://docs.ansible.com/ansible/latest/playbooks_best_practices.html#directory-layout

### Set-up

Ansible uses an inventory file to determine what hosts to work against. This file is stored by default at '/etc/ansible/hosts', although a bespoke path can be set using an option `–inventory=/path/to/inventory/file`.


### Sample commands

```
# Ping a server
ansible all --inventory-file=inventory.ini --module-name ping -u root
ansible all -i inventory.ini --m ping -u root

# Remotely execute code
ansible all -i inventory.ini -m command -u root --args "uptime"
ansible all -i inventory.ini -m command -u root -a "uptime"

# Execute a playbook
ansible-playbook path/to/playbook.yml -i inventory.ini -u root
```

#### No SSH key added yet?

Use the `-k` flag to prompt for an SSH password. This will typically be required the first time you login - for example:

```
ansible-playbook path/to/playbook.yml -i inventory.ini -u root -k
```

#### Want to add a SSH key?

```
ansible-playbook configure.yml -i servers.ini
```

## Data Snapshot Backup Server

To set up a server to back up the Data Snapshot used by the Dashboard.

**TODO:** Fix TODO in `roles/backup-data-snapshot/tasks/backup-data-snapshot.yml`

```
# create a `servers-dev.ini` file detailing the required servers
ansible-playbook backup-data-snapshot.yml -i servers-dev.ini
```
