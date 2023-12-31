---
title: Bare Metal Arch Linux Installation
date: 2023-11-22T16:00:00Z
description: |-
  A guide for the initial installion and configuration of Arch Linux on new
  bare-metal systems which will form part of the Lab environment.
summary: |-
  A guide for the configuration, installation, and set up, of all the various
  components for new bare-metal systems, including UEFI, Secure Boot, Boot
  Loaders, the Operating System, and Ansible plays, to prepare a new or wiped
  system back to full operation.
tags:
  - documentation
  - guide
  - bare-metal
  - ansible
  - secure-boot
---

This page provides a guide to the configuration, installation, and set up, of
all the various components for new bare-metal systems, including UEFI, Secure
Boot, Boot Loaders, the Operating System, and Ansible plays, to prepare a new or
wiped system back to full operation.

## Initial Configuration

The first steps are to boot the system using an Arch Linux USB or ISO image, and
prepare it to receive remote connections over SSH so that Ansible can run the
[`bootstrap`][play-bootstrap].

{{< alert "power-off" >}} Everything in this guide assumes that the [Bare Metal
Server Configuration]({{< ref "bare-metal-setup.md" >}}) steps have been
completed, and as such the UEFI is ready and Secure Boot has been disabled so
that the Arch Linux image can boot.{{< /alert >}}

- Given _Secure Boot_ is disabled in the UEFI for this system, simply plug in
  the USB to the host, along with a keyboard and monitor, and power on. The USB
  will normally be the only bootable device, and the UEFI will therefore select
  this by default, booting into the Arch Linux installation environment.

  If this is not the case, press `Ctrl` + `F10` during the first second of power
  on to select the boot device menu from the UEFI (entering the Supervisor
  password when asked) and select the USB device from the available list.

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

  Take note of the link-local (`scope link`) address on the `enp86s0` interface
  above, and specifically anything after the `fe80::` prefix. This part of the
  address is the EUI64 address for the interface and will need to be appended to
  the network prefix (see below). It will be important to get both the IPv4 and
  IPv6 addresses are correct to ensure that the network settings are correctly
  configured by Ansible in all future runs.

  Once ready, then add all IPv4 and IPv6 settings as follows:

  ```console
  $ ip link add link {dev} name {dev}.32 type vlan id 32
  $ ip -4 addr add 172.23.32.{number}/24 dev {dev}.32
  $ ip -4 route add 0.0.0.0/0 via 172.23.32.1
  $ ip -6 addr add 2a02:8010:8006:3a32:{eui64-address}/64 dev {dev}.32
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
  $ task baseline limit=node-01.s.cym-south-1.kub3.uk
  BECOME password: {password}
  ```

[n3tuk-ansible]: https://github.com/n3tuk/ansible
[play-bootstrap]:
  https://github.com/n3tuk/ansible/blob/main/plays/bootstrap.yaml
[taskfile]: https://github.com/n3tuk/ansible/blob/main/Taskfile.yaml
[inventory]: https://github.com/n3tuk/ansible/blob/main/inventory.yaml
[hosts-vars]: https://github.com/n3tuk/ansible/tree/main/plays/hosts_vars

## Secure Booting

To optimise the security of the system, Secure Boot should be enabled to verify
that the kernel and initial images have not been tampered with, and as such
allow the encrypted filesystem to be unlocked automatically (especially
important as this is a remotely-managed server).

Although the [bootstrapping](#bootstrapping) steps above ensure that the keys
are configured, and the kernels are set up and signed with them, we need to
prepare the system to accept the public keys and then enable Secure Boot.

- Once successfully run, reboot the system and press `Ctrl` and `F2` to access
  the UEFI interface, entering in the required password for the Supervisor on
  this system.

- Press `Tab` until _Boot_ is highlighted, and press `Enter`, then go `down` and
  select _Reset to Setup Mode_, confirming with _Yes_ on the pop-up. This will
  allow the Secure Boot keys to be enrolled into the UEFI BIOS and validate
  signed kernels for booting. Press `Ctrl` + `F10` then select _Yes_ to save the
  settings and reboot.

- Once the system has been rebooted, unlock the storage partition (locally), log
  in as the `root` account (either directly via the console, or remotely via a
  user account who can `sudo` into the `root` account. From there, run `sbctl`
  to verify Secure Boot is ready to be loaded with our custom keys:

  ```console
  $ sbctl status
  Installed:      ✓ sbctl is installed
  Owner GUID:     00000000-0000-0000-0000-000000000000
  Setup Mode:     ✗ Enabled
  Secure Boot:    ✗ Disabled
  Vendor Keys:    none
  ```

  So long as _Setup Mode_ is showing as _Enabled_, run the `sbctl enroll-keys`
  command to load the locally generated keys into the database, ensuring that
  the Microsoft keys are enrolled too:

  ```console
  $ sbctl enroll-keys --microsoft
  Enrolling keys to EFI variables...
  With vendor keys from microsoft...✓
  Enrolled keys to the EFI variables!
  ```

- To verify this has worked as expected, again reboot the system and press
  `Ctrl` and `F2` to access the UEFI interface, entering in the required
  password for the Supervisor on this system.

- Press `Tab` until _Boot_ is highlighted, and press `Enter`, then go `down` and
  select _Secure Boot_, changing _Secure Boot_ in the following screen to
  _Enabled_. Press `Ctrl` + `F10` then select _Yes_ to save the settings and
  reboot.

- The system should boot as normal, but with Secure Boot enabled. If there is a
  warning message about the authenticity of the boot loader or kernels, then
  disable Secure Boot in the UEFI following the above steps, and then reboot in
  the system and run:

  ```console
  $ sbctl sign-all
  ✓ Signed /efi/EFI/Linux/linux-hardened.efi
  ✓ Signed /efi/EFI/Linux/linux-lts.efi
  ```

  To verify that Secure Boot is fully operational and the signed kernels are
  loaded correctly, and that _Setup Mode_ has been disabled (so no new keys can
  be added), run `sbctl status`:

  ```console
  $ sbctl status
  Installed:      ✓ sbctl is installed
  Owner GUID:     00000000-0000-0000-0000-000000000000
  Setup Mode:     ✓ Disabled
  Secure Boot:    ✓ Enabled
  Vendor Keys:    microsoft
  ```

## TPM Enrollment

Once we are booting with Secure Boot enabled, the system is ready to enroll the
TPM chip into the boot process by storing an encrypted key to unlock the
encrypted filesystem, so long as nothing has been tampered. The following steps
will accomplish this.

- The next step is to enroll the encrypted filesystem into the TPM module on the
  system, which allows the UEFI/TPM module to provided the decryption key for
  the filesystem, so long as the system has not been tampered and the kernels
  are signed.

  First, verify that the TPM device is available to be configured:

  ```console
  $ systemd-cryptenroll --tpm2-device=list
  PATH        DEVICE      DRIVER
  /dev/tpmrm0 MSFT0101:00 tpm_crb
  ```

  ```console
  $ systemd-cryptenroll \
      --tpm2-device=auto \
      --tpm2-pcrs=0+7 \
      /dev/nvme0n1p2
  Please enter current passphrase for disk /dev/nvme0n1p2: {passphrase}
  New TPM2 token enrolled as key slot 1.
  ```

  The TPM2 should now be enrolled and the system can boot with a signed kernel
  only and decrypt the filesystem provided there's been no change to the UEFI
  firmware and Secure Boot remains enabled. The `/etc/crypttab` files already
  support TPM2 unlocking, so no further configuration is required.

## Baselining

Once a system has been bootstrapped and configured for secure booting, we can
run the [`baseline`][play-baseline] play against the server. This ensures that
all final configurations are made and anything which should not be installed
during bootstrapping is installed now.

- The final step is to run the [`baseline`][play-baseline] play from the
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
  $ task baseline limit=node-01.s.cym-south-1.kub3.uk
  BECOME password: {password}
  ```

  This will ensure that the final utilities and configurations are installed
  (such as `firewalld`), and then that the system is ready to operate as
  required (running the appropriate plays for the specific services and
  configurations needed).

[play-baseline]: https://github.com/n3tuk/ansible/blob/main/plays/baseline.yaml

- Reboot the system to verify everything operates as expected.
