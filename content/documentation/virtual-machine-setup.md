---
title: Virtual Machine Server Configuration
date: 2023-11-28T16:00:00+00:00
description: |-
  Documentation on the set up and configuration of virtual machine servers in
  the n3tuk Organisation.
summary: |-
  Documentation on the set up and configuration of the virtual machine-based
  servers used in the `n3tuk` Organiation for the hosting of all systems and
  services. This includes the installation of the operating system, filesystem
  layout, etc..
tags:
  - documentation
  - virtual-machines
  - installation
  - ansible
  - arch-linux
draft: true
---

{{< alert "edit" >}} This document is currently just a placeholder; it has yet
to be written.<br />Please wait for it to be updated. {{< /alert >}}

## Sub-Documents

| Document                                                                                      | Description                                                                                                                                   |
| :-------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------- |
| [Partitions]({{< ref "virtual-machine-setup/partitions.md" >}})                               | A breakdown of the partitions layout for all the virtual machines.                                                                            |
| [Arch&nbsp;Linu&nbsp;Installation]({{< ref "virtual-machine-setup/arch-linux-install.md" >}}) | A step-by-step guide for the initial configuration of Arch Linux on virtual machines using commands in the live-boot environment, and Ansible |

## Initial Configuration

All virtual machines in the Lab environment are managed through Ansible, which
will configure the QEMU instance through `libvirtd` and boot it using the Arch
Linux installation image, ready for bootstrapping.

### Ansible

There are two components to the creation of a Virtual Machine:

1. The configuration of the virtual machine itself on the node that will host
   it; and
1. The configuration of the basic configuration of that host within Ansible
   itself so that it can be bootstrapped, baselined, and the relevant services
   deployed.

#### Create the Virtual Machine

Each virtual machine is allocated to a specific host as this Lab environment
does not support pools, but this is intentional as so to be able to treat nodes
as availability zones and network segments, providing a pseudo CSP environment
for testing.

Under the `plays/host_vars` directory of the [`ansible`][ansible] repository are
multiple files related to each of the virtual machine hosts (called
`node-xx.y.z.kub3.uk.yaml`, where `xx` is the number of the node, `y` is the
initial of the environment, and `x` is the location). Within this file is a list
called `machines_list`, to which a virtual machine configuration can be
appended:

[ansible]: https://github.com/n3tuk/ansible

```yaml
# Create a virtual machine called name-01 in the development environment of
# cym-south-1. Each of these help define the hostname and.
- name: name-01
  environment: development
  location: cym-south-1
  # Optionally, add the purpose of the node which will be added to the node
  # title in libvirtd for quick reference.
  purpose: boundary-node
  # Define the number of vCPUs and the amount of memory required. The current
  # argument defines how much it will run with, and if it should be allowed to
  # increase, max: can be set to the maximum amount it can grow to.
  vcpu: 1
  memory:
    current: 1
    # Allow the memory to be shared across nodes through overcommitting
    shared: true
  # Define the volumes which should be attached to the virtual machine, their
  # name, and their size.
  volumes:
    - dev: vda
      size: 32G
  # Which VLAN should be used for the ethernet device on this virtual host,
  # effectively defining which bridge it should be attached to.
  vlan: 50
```

This can be deployed using the `libvirtd` task:

```console
$ task libvirtd limit={node-hostname}
```

#### Configure Ansible

Once the virtual machine is ready to be bootstrapped, one file needs to be
updated (the [`inventory.yaml`][inventory] file) and two need to be added: (one
under [`plays/host_vars`][host-vars], and optionally one under
[`plays/group_vars`][group-vars], depending on the inventory configuration).

First, inside the [`inventory.yaml`][inventory] file, add an entry under one of
the `hosts:` maps, or create a `{name}.hosts` key under `all.children` where
`{name}` is the name of a new group. Also add it to one of `services.hosts`,
`production.hosts`, or `development.hosts` depending on the environment it
belongs to.

[inventory]: https://github.com/n3tuk/ansible/blob/main/inventory.yaml
[host-vars]: https://github.com/n3tuk/ansible/tree/main/plays/host_vars/
[group-vars]: https://github.com/n3tuk/ansible/tree/main/plays/group_vars/

Next, create a file called `plays/host_vars/{hostname}.yaml` which contains, at
a minimum, the IPv4 and IPv6 addresses of the host being configured:

```yaml
---
systemd_networkd_ipv4_address: {ip-address}/{prefix}
systemd_networkd_ipv4_gateway: {ip-address}
systemd_networkd_ipv6_address: {ip-address}/{prefix}
```

If an additional group configuration is required, create that too. Once done,
the virtual machine can be bootstrapped.

### Bootstrapping

To bootstrap the new virtual machine, follow through the steps in the [Arch
Linux Installation]({{< ref "virtual-machine-setup/arch-linux-install.md" >}})
guide.
