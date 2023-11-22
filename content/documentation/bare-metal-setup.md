---
title: Bare Metal Server Configuration
date: 2023-08-12T14:00:00+01:00
description: |-
  Documentation on the set up and configuration of bare-metal servers in the
  n3tuk Organisation.
summary: |-
  Documentation on the set up and configuration of the bare-metal servers used
  in the `n3tuk` Organiation for the hosting of all systems and services. This
  includes the installation of the operating system, filesystem layout, and
  managing encryption-at-rest.
tags:
  - documentation
  - bare-metal
  - installation
  - ansible
  - arch-linux
---

{{< alert "edit" >}} This document is currently just a placeholder; it has yet
to be written.<br />Please wait for it to be updated. {{< /alert >}}

## Sub-Documents

| Document                                                                                 | Description                                                                                                                             |
| :--------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------- |
| [Partitions]({{< ref "bare-metal-setup/partitions.md" >}})                               | A breakdown of the partitions layout for all bare metal machines.                                                                       |
| [Arch&nbsp;Linu&nbsp;Installation]({{< ref "bare-metal-setup/arch-linux-install.md" >}}) | A step-by-step guide for the initial configuration of Arch Linux on the server using commands in the live-boot environment, and Ansible |

## Initial Configuration

### UEFI Update

Using a USB Flash Drive, take a copy of the latest UEFI Firmware release from
Intel and copy the `.cap` file to the root of the flash drive, and plug it into
the Intel NUC.

1. Power on the Intel NUC and regularly press `F7` until the UEFI (BIOS) Flash
   Update screen is shown.
1. Using the arrow keys, select the USB Flash Drive from the menu and press
   return. The `.cap` file should now be shown, press return with the `.cap`
   file selected.
1. Press enter on `Yes` to confirm that we wish to update the BIOS with the
   `.cap` file.
1. The Intel NUC should now reboot and go through the firmware update. The
   screen may go dark for a while before a progress meter shows. The whole
   process should take about five minutes.

### UEFI Settings

There are a number of standard settings to be configured for the Intel NUC which
should be done during initial setup, as it's connected to a monitor, including
things such as controlling fan speeds, power light brightness, and disabling
secure boot for installation.

{{< alert "power-off" >}} When entering the UEFI, check the `Version` at the top
of the screen matches the version loaded. For example, with the `AN0027.cap`
file, you should see `ANRPL357.0027.{year}...` as the version. If this is not
the case, re-run the UEFI update above. {{< /alert >}}

1. Power on the Intel NUC and regularly press `F2` until the UEFI Settings
   screen is shown.

1. There are no changes to be made under `Main`, but move down and check the
   `System Date` and `System Time` values. If they are not correct, update them
   to be close to the current time, making it easier for NTP to set them later.

   {{< alert "clock" >}} The Date/Time should be based on UTC and not BST (if in
   the summer) as the system will apply the configured Timezone over the top of
   this. {{< /alert >}}

1. Move up and then right to select `Advanced`:

   1. For `STORAGE`:

      1. `SATA Controllers` is `un-checked`
      1. `SMART Self Test` is `checked`
      1. `M.2 Slot 1` is `checked`
      1. `HDD Activity LED` is `checked`
      1. `Enable VMD controller` is `un-checked`

   1. For `Onboard Devices`:

      1. `HD Audio` is `Disabled`
      1. `LAN` is `checked`
      1. `Thunderbolt Support` is `checked` (this is needed for access to the
         monitors via the thunderbolt supports on the rear of the Intel NUC)
      1. `WLAN` is `un-checked`
      1. `Bluetooth` is `un-checked`
      1. `Gaussian Mixture Modules` is `checked`
      1. `HDMI CEC Control` is `un-checked`
      1. `High Precision Event Timers` is `checked`
      1. `Bootup Numlock State` is `On`
      1. `Failsafe Watchdog` is `checked`

   1. For `USB`:

      1. `Portable Device Charging Mode` is `Off`
      1. `Front USB Charging Port` to `No Detect` (this will disable the port
         for the UEFI Firmware, but enable it for the Operating System)
      1. `Front Type A Port` to `Enabled`
      1. `Rear Upper Type A Port` and `Rear Lower Type A Port` to `No Detect`
      1. `Internal USB Header Connection 1` and
         `Internal USB Header Connection 2` to `Disabled` (Prevent access to the
         Operating System)
      1. `USB via M.2 2242 Slot` to `Disabled`
      1. `Front Type A Port Power On/Off` to `Enabled`
      1. `Rear Upper Type A Port Power On/Off` and
         `Rear Lower Type A Port Power On/Off` to `Enabled`
      1. `Internal USB Header Connection 1 Power Power On/Off` and
         `Internal USB Header Connection 2 Port Power On/Off` to `Disabled`
      1. `USB via M.2 2242 Slot Power On/Off` to `Disabled`

   1. For `Video`:

      1. `IGD Minimum Memory` to `64 MB`
      1. `IGD Aperture Size` to `256 MB`
      1. `IGD Primary Video Port` to `Auto`
      1. `Screen Rotation Policy` to `Normal`
      1. `Display Emulation` to `Virtual display emulation`
      1. `Clear Software Control State` to `No`

   1. For `Add-in Config`:

      1. There is nothing to set or change here.

   1. `Auto RTC Reset` to `checked`

   1. `Force-enable power to 2242 m.2 slot` to `un-checked`

   1. `BIOS WU Support` to `un-checked` (Windows Update Support is not required)

1. Move up and then right to select `Power, Performance and Cooling`:

   1. `External Ambient temperature Tolerance` to `35oC`

   1. For `Secondary Power Settings`:

      1. `Button LED` to `Power State Indicator`
      1. `S0 Indicator Brightness` to `25`
      1. `S0 Indicator Blinking Behavior` to `Breathing`
      1. `S0 Indicator Blinking Frequency (Hz)` to `0.1` (every 10 seconds)
      1. `S0 Indicator Color` to `Blue`
      1. `Modern Standby Brightness` to `20`
      1. `Modern Standby Blinking Behavior` to `Solid`
      1. `Modern Standby Color` to `Amber`
      1. `Power Sense` to `checked`
      1. `After Power Failure` to `Power On`
      1. `Deep power saving mode (Pseudo G3)` to `un-checked` (we need WOL)
      1. `Wake on LAN from S4/S5` to `Power On - Normal Boot`
      1. `Wake System from S5` to `un-checked`
      1. `USB S4/S Power` to `un-checked` (Do not provider power to USB ports in
         sleep)
      1. `Wake from Thunderbolt Devices` to `un-checked`
      1. `PCIe ASPM Support` to `checked`
      1. `Native ACPI OS PCIe Support` to `checked`

   1. `Fan Mode` to `Balanced`

   1. `Fan Control Mode` to `Automatic Fan Control Mode`

1. Move up and then right to select `Security`:

   1. Press `Enter` on `Supervisor Password` and enter a password for Supervisor
      access to the UEFI Settings, ensuring that it is saved under
      `personal/servers/{hostname}/uefi/supervisor` for the Password Store.
      Press `Enter` on `Ok`.

   1. `User Access Level` to `View Only`

   1. For `Security Features`:

      1. `Allow UEFI 3rd party driver loaded` to `un-checked`
      1. `Intel Virtualisation Technology` to `checked`
      1. `Intel VT for Directed I/O (VT-d)` to `checked`
      1. `Intel Platform Trust Technology` to `checked`
      1. `iSetupCfg Password Check` to `Enabled`

1. Move up and then right to select `Boot`:

   1. For `Secure Boot`:

      1. `Secure Boot` to `Disabled`
      1. `Secure Boot Mode` to `Custom`

   1. For `Boot Priority`:

      1. `Fast Boot` is `un-checked`
      1. `Boot USB Devices First` to `un-checked`
      1. `Boot Network Devices Last` to `checked`
      1. `Unlimited Boot to Network Attempts` to `un-checked`
      1. `BIOS Setup Auto-Entry` to `un-checked`
      1. `Internal UEFI Shell` to `un-checked`
      1. `USB` and `Optical` to `checked` (Optical needed where the USB image is
         based on a `.iso` format even though it's not an actual optical drive)
      1. `Network Boot` to `Disabled`

   1. For `Boot Display Configuration`:

      1. `BIOS Self Recovery` to `un-checked`
      1. `Suppress Alert Messages at Boot` to `un-checked`
      1. `POST Function Hotkeys Displayed` to `checked`
      1. `Display F2 to Enter Setup` to `checked`
      1. `Display F7 to Update BIOS` to `checked`
      1. `Display F10 to Enter Boot Menu` to `checked`
      1. `Display F12 for Network Boot` to `checked`
      1. `POST Logo` to `un-checked`
      1. `BIOS Self-Help` to `un-checked`

1. Press `F10` to `Save & Exit Setup`. Press `Enter` on `Ok`.
