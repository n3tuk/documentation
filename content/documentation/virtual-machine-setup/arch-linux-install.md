---
title: Virtual Machine Arch Linux Installation
date: 2023-11-28T16:00:00Z
description: |-
  A guide for the initial installion and configuration of Arch Linux on new
  virtuam machine which will form part of the Lab environment.
summary: |-
  A guide for the configuration, installation, and set up, of all the various
  components for new virtuam machine, including UEFI, Secure Boot, Boot
  Loaders, the Operating System, and Ansible plays, to prepare a new or wiped
  system back to full operation.
tags:
  - documentation
  - guide
  - virtual-machines
  - ansible
  - secure-boot
---

This page provides a guide to the configuration, installation, and set up, of
all the various components for new virtual machines, Boot Loaders, the Operating
System, and Ansible plays, to prepare a new or wiped system back to full
operation.

## Initial Configuration

The first steps are to boot the system using an Arch Linux USB or ISO image, and
prepare it to receive remote connections over SSH so that Ansible can run the
[`bootstrap`][play-bootstrap]. The booting is managed by the Ansible play and
role which created the virtual machine.

- Once booted and logged into the `root` console, set the hostname for the
  device using `hostnamectl`:

  ```console
  $ hostnamectl hostname {hostname}
  ```

- Configure the initial networking with `ip` to provide the node with access to
  the `kub3-node` VLAN and default routing for access for installation:

  ```console
  $ ip addr
  1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
      link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
      inet 127.0.0.1/8 scope host lo
         valid_lft forever preferred_lft forever
      inet6 ::1/128 scope host noprefixroute valid_lft forever
         preferred_lft forever
  2: enp86s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9000 qdisc mq state UP group default qlen 1000
      link/ether 48:21:eb:55:83:7b brd ff:ff:ff:ff:ff:ff
      inet6 fe80::4821:ebff:fe55:837b/64 scope link proto kernel_ll
         valid_lft forever preferred_lft forever
  ```

  Take note of the link-local (`scope link`) address on the `enp3s0` interface
  above, and specifically anything after the `fe80::` prefix. This part of the
  address is the EUI64 address for the interface and will need to be appended to
  the network prefix (see below). It will be important to get both the IPv4 and
  IPv6 addresses are correct to ensure that the network settings are correctly
  configured by Ansible in all future runs.

  Once ready, then add all IPv4 and IPv6 settings as follows:

  ```console
  $ ip link add link {dev} name {dev}.{vlan} type vlan id {vlan}
  $ ip -4 addr add 172.23.{vlan}.{number}/24 dev {dev}.{vlan}
  $ ip -4 route add 0.0.0.0/0 via 172.23.{vlan}.1
  $ ip -6 addr add 2a02:8010:8006:3a{vlan}:{eui64-address}/64 dev {dev}.{vlan}
  ```

  {{< alert "location-dot" >}} The IPv6 route will automatically received any
  processed by the Router Advertisements sent by `router-lan-01` and can be
  verified by checking with the following command: {{< /alert >}}

  ```console
  $ ip -6 route
  ::/64 dev wlan0 proto ra metric 256 expires 2591909sec pref medium
  default via fe80::7a9a:18ff:fe49:b230 dev wlan0 proto ra metric 256 expires 3509sec pref high
  ```

- Set the `root` user password on the live installation image to allow remove
  access for configuration using Ansible:

  ```console
  $ passwd
  New password: {password}
  Retype new password: {password}
  password: password updated successfully
  ```

## Bootstrapping

The bootstrapping of the system is the biggest part of the configuration of the
host and is fully automated. It should take about 2-3 minutes to complete on
each host and will set up all filesystems, bootstrap the operating system, and
pre-configure the host ready to be rebooted into itself.

- Start an SSH session to the `root` user on the host to be installed, but
  ensure that the Host Key is neither saved nor verified for this host as it
  will be using a temporary one generated by the installation image:

  ```console
  $ ssh \
      -o StrictHostKeyChecking=no \
      -o UserKnownHostsFile=/dev/null \
      {hostname}
  root@{hostname} ~ #
  ```

  OR to test multiple hosts at the same time, use the `ansible.builtin.ping`
  module with the group to run a parallel check:

  ```console
  $ ansible all \
      --inventory inventory.yaml
      --module-name ansible.builtin.ping \
      --ask-pass --user root --limit {group}
  ```

- Taking a clone of the [Ansible][n3tuk-ansible] repository, run the
  [`bootstrap`][play-bootstrap] using the [`Taskfile.yaml`][taskfile]. If this
  system is new to the n3tuk Lab, then ensure that the host is listed in the
  [`inventory.yaml`][inventory] file, as well as having it's own variables file
  under [`hosts_vars`][hosts-vars].

  ```console
  $ task bootstrap
  SSH password: {password}

  PLAY [Bootstrap new physical and virtual machines with Arch Linux] *************
  ```

  If the `bootstrap` play only needs to be focused on a single host, or selected
  hosts, then the `limit=` argument can be provided on the command-line to only
  run on those hosts:

  ```console
  $ task baseline limit=vault:cache-01.s.cym-south-1.kub3.uk
  BECOME password: {password}
  ```

[n3tuk-ansible]: https://github.com/n3tuk/ansible
[play-bootstrap]:
  https://github.com/n3tuk/ansible/blob/main/plays/bootstrap.yaml
[taskfile]: https://github.com/n3tuk/ansible/blob/main/Taskfile.yaml
[inventory]: https://github.com/n3tuk/ansible/blob/main/inventory.yaml
[hosts-vars]: https://github.com/n3tuk/ansible/tree/main/plays/hosts_vars

## Baselining

Once a system has been bootstrapped, we can run the [`baseline`][play-baseline]
play against the server. This ensures that all final configurations are made and
anything which should not be installed during bootstrapping is installed now.

- First, ensure that the server is rebooted, allowing the newly-installed
  operating system to run the system, which will have the required user accounts
  and `sudo` permissions to run Ansible in its normal operation.

  To verify that all the servers and accounts are working as expected, use the
  `ping` task to test the ability of Ansible to connect to the server and `sudo`
  to the `root` user:

  ```console
  $ task ping limit={names}
  Keys:
        OK          DNS
        No Route    Timeout
        Refused)    Denied

  Pinging all known hosts in the inventory to test for access...
  BECOME password: {password}
     vault-01.p.cym-south-1.kub3.uk
     vault-03.d.cym-south-1.kub3.uk
     vault-01.s.cym-south-1.kub3.uk
     vault-01.d.cym-south-1.kub3.uk
     vault-02.p.cym-south-1.kub3.uk
     vault-03.p.cym-south-1.kub3.uk
     vault-02.d.cym-south-1.kub3.uk
  ```

- Next, run the [`baseline`][play-baseline] play from the
  [Ansible][n3tuk-ansible] repository using the [`Taskfile.yaml`][taskfile]:

  ```console
  $ task baseline
  BECOME password: {password}

  PLAY [Baseline all physical machines] ******************************************
  ```

  Like with the `bootstrap` play above, if `baseline` only needs to be focused
  on a single host, or selected hosts, then the `limit=` argument can be
  provided on the command-line to only run on those hosts:

  ```console
  $ task baseline limit={vault}
  BECOME password: {password}
  ```

  This will ensure that the final utilities and configurations are installed
  (such as `firewalld`), and then that the system is ready to operate as
  required (running the appropriate plays for the specific services and
  configurations needed).

[play-baseline]: https://github.com/n3tuk/ansible/blob/main/plays/baseline.yaml

- Reboot the system to verify everything operates as expected.