# Configurations for OneClickDesktop

This repository contains ansible playbooks examples to help configure systems before deployment of the system.
Basicly it describes all dependencies for machines running Virtualization Server and Overseer applications.
Before any actions please read this instruction.

# Requirements for playbooks

All playbooks was written for Arch Linux based machines.
Machine to sonfiguration must have internet connection and ssh connection for user with superuser privileges.

For other systems feel free to correct playbooks for your system with instructions written below.

# Folder structure

Configuration scripts can be found in folder [playbooks](playbooks). There should be common, virt-srv and overseer configuration playbooks.
They can be runned by scripts from [scripts](scripts) directories.
But before any actions check the [inventory](inventory) directory, which contains access information to machines.

Also feel free to extend this structures about new groups and machines. It is only a simple example of systems contains from 2 types of machines. But some steps in playbooks are important and are described below.

# Common configuration
Defined in file [common_playbook.yml](playbooks/common_playbook.yml).

## Important steps

All overseers and virtualization server capable machines must have:
1. `docker` and `docker-compose` installed to run applications.
2. `git` installed to clone code from which containers can be build.
3. Enabled and started docker service.
4. Created executive_user with docker group attached.

## Important variables

This group has configuration [file](inventory/group_vars/all.yml), which contains user who will run the whole system. By default it's named `ocdesk` and is arbitraly chosen.
```
executive_user: ocdesk
```

## Common variables for every virtualization server and overseer

To conenct with machine ansible has to know what address and what credentials should be used to communicate. In both groups we define variables:

```
ansible_port: 22
ansible_host: 10.20.30.40
ansible_user: ansible
ansible_ssh_private_key_file: path/to/private/ssh/key
ansible_ssh_pass: password_ssh
ansible_become_password: password_become
```

More informations about ssh credetials can be found at ansible [documentation](https://docs.ansible.com/ansible/latest/user_guide/connection_details.html). For configuration important is `ansible_become_password` becouse of root privileged actions.

# Overser configuration
Defined in file [main_playbook_overseer.yml](playbooks/overseer/main_playbook_overseer.yml).

## Important steps

All Overseer capable machine must have:
1. Cloned [overseer repository](https://github.com/one-click-desktop/overseer) to folder owned by `executive_user`.

How to build and run Overseer application is described at [overseer repository's](https://github.com/one-click-desktop/overseer) README.

Overseer doesn't need any special services running at configured system.

## Important variables

All important variables all defined at [common variables section](#common-variables-for-every-virtualization-server-and-overseer)

# Virtualization Server configuration
Defined in file [main_playbook_virtsrv.yml](playbooks/virtsrv/main_playbook_virtsrv.yml).

## Important steps

## Important variables