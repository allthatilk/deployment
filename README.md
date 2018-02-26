# Deployment

A repository for managing deployment processes. Single-purpose successor to https://github.com/IATI/IATI-Websites

## Ansible test

Based on guide at http://ryaneschinger.com/blog/ansible-quick-start/

### Set-up

Ansible uses an inventory file to determine what hosts to work against. This file is stored by default at '/etc/ansible/hosts', although a bespoke path can be set using an option `–inventory=/path/to/inventory/file`.

Ansible 2.4 is a minimum requirement due to the use of the `include_tasks` syntax.

The Datastore playbook was only tested on Ubuntu 16.04.3 x64 for our internal use. Compatibility with other systems is not guaranteed.


### Sample commands

```
# Ping a server
ansible all -i servers.ini -m ping -u root

# Remotely execute code
ansible all -i servers.ini -m command -u root -a "uptime"

# Execute a playbook
ansible-playbook path/to/playbook.yml -i servers.ini -u root
```

#### No SSH key added yet?

Use the `-k` flag to prompt for an SSH password. This will typically be required the first time you login - for example:

```
ansible-playbook path/to/playbook.yml -i servers.ini -u root -k
```

#### Want to add a SSH key?

```
ansible-playbook configure.yml -i servers.ini
```
