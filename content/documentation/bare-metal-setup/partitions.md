---
title: Bare Metal Machine Partitions
date: 2023-08-12T14:00:00+01:00
description: |-
  An overview of the partions required for the bare-metal machines in the n3tuk
  Organisation.
summary: |-
  An overview of the partions and the layout required for the bare-metal
  machines in the `n3tuk` Organisation, with information on their purpose and
  any additional details about monitoring and setup.
tags:
  - documentation
  - bare-metal
  - partitions
---

## Common Partitions

The following represents the default partition layout for all the bare-metal
machines deployed.

| Partition          | Type           |        Size | Becomes             |
| :----------------- | :------------- | ----------: | :------------------ |
| `/dev/vda1`        | `fat32` (boot) |       ~1GiB | `/efi`              |
| `/dev/vda2`        | LVM            | (remaining) | `lvm:storage`       |
| `storage:system`   | `ext4`         |        8GiB | `/`                 |
| `storage:pacman`   | `ext4`         |        2GiB | `/var/cache/pacman` |
| `storage:journald` | `ext4`         |        1GiB | `/var/log/journal`  |
| `storage:home`     | `ext4`         |        1GiB | `/home`             |
| `storage:swap`     | `swap`         |        8GiB | `swap`              |

{{< alert "sensor-triangle-exclamation" >}} Monitoring for the available
capacity on the `/var/log/journal` and `/var/cache/pacman` partitions will
normally be disabled as they will operate at close to full in typical operation.
{{< /alert >}}
