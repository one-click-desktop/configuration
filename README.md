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

## Important overseer variables

All important variables all defined at [common variables section](#common-variables-for-every-virtualization-server-and-overseer)

# Virtualization Server configuration
Defined in file [main_playbook_virtsrv.yml](playbooks/virtsrv/main_playbook_virtsrv.yml).

Virtualization server requires a lot of additional services running at machine. After

## Packages

All virtualization server cable machines must have installed packages:
1. `qemu` virtualization driver.
2. `libvirt` with libvirt daemon to hypervise qemu/kvm virtual machines.
3. `vagrant` with `vagrant-libvirt` plugin to crate replicable machines (`vagrant-libvirt` plugin version should be at least 0.7.0 - use `bagrant plugin install` for instalation).
4. `dnsmasq` for mapping virtual machines network
5. Some firewall software to isolate virtual machines network (`ufw` is preffered - system was tested with it).
6. `ansible` to configure virtual machines after stratup.
7. `bridge-utils` for virtual machines networking configurations.

## Bridge interface
To correctly start virtual machine as a part of the system it must have bridge interface attachech. This bridge device have to connect those virtual machines with network used to communicate with clients.

Bridge can be made for many diffrent ways. Firstly we have to choose one physical network interface as a bridge interface. Then create bridge device with this interface attached to it.

In playbook bridge is configured with `network-manager`. To correctly run script user have to define(variable names defined at [variables](#important-virtualizatoin-server-variables)):
1. Name of chosen physical interface (eg. eth1)
2. Connection paired with physical interface
3. Name of createad bridge device.

After running script slave device has to be disabled and bridge connection has to be enabled by user.

## Firewall configuration

Virtual machines running as a part of the system are connected to 2 networks: configuration and access.
Configuration netwrk is created by vagrant to correctly start virtual machine.
Access network is for rdp connection and is realized by bridge device passed to virtual machine.

Because of docker and vagrant firewall configuration virtual machines are blocked at access network.
At this situation started machines typicaly doesn't aqcuire address from DHCP at bridge connected interface.
To resolve this problem firewall should be configured to pass every packets from bridged devices.
Simply add this rule on top of iptables FORWARD chain:
```
-I FORWARD -m physdev --physdev-is-bridged -j ACCEPT
```

## Application and services

Before running application `libvirtd` service should be enabled and started.
In addition `executive_user` should be part of `libvirt` and `kvm` group.
Also make sure, that `executive_user` has in his home folder `.vagrant.d` where system will be storing template images of vagrant boxes.

At the end application should be cloned to folder owned by `executive_user`.

How to build and run Virtualization Server application is described at [virtualization-server repository's](https://github.com/one-click-desktop/overseer) README.

Virtualization server requires `libvirtd` service running at system to operate correctly.


## Important virtualizatoin server variables

In addition to [common variables](#common-variables-for-every-virtualization-server-and-overseer) virtualization server playbook requires inforamtions about bridge connections.
User must declare:
1. `bridge_name` - name of bridge device to create
2. `bridge_slave_interface` - interface name of physical interface to bridge
3. `slave_connection` - name of Networkmanager connection associated with bridging device declared at `bridge_slave_interface`.

For eg.
```
bridge_name: br0
bridge_slave_interface: enp2s0
slave_connection: Wired\ connection\ 1
```
