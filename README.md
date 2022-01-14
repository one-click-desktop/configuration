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

All overseers and virtual server capable machines must have:
1. `docker` and `docker-compose` installed to run applications.
2. `git` installed to clone code from which containers can be build.
3. Enabled and started docker service.
4. Created executive_user with docker group attached.

## Important variables

This group has configuration [file](inventory/group_vars/all.yml), which contains user who will run the whole system. By default it's named `ocdesk` and is arbitraly chosen.
```
executive_user: ocdesk
```

# Overser configuration

# Virtualization Server configuration

