---
title: Virtual Machine Partitions
date: 2030-01-01T00:00:00+00:00
description: |-
  An overview of the partions required for the virtual machines in the n3tuk
  Organisation.
summary: |-
  An overview of the partions and the layout required for the virtual machines
  in the `n3tuk` Organisation, with information on their purpose and any
  additional details about monitoring and setup.
tags:
  - documentation
  - virtual-machines
  - partitions
draft: true
---

## Common Partitions

The following represents the default partition layout for all the virtual
machines deployed.

| Partition          | Type           |        Size | Becomes             |
| :----------------- | :------------- | ----------: | :------------------ |
| `/dev/vda1`        | `fat32` (boot) |     ~256GiB | `/efi`              |
| `/dev/vda2`        | LVM            | (remaining) | `lvm:storage`       |
| `storage:system`   | `ext4`         |        8GiB | `/`                 |
| `storage:pacman`   | `ext4`         |        2GiB | `/var/cache/pacman` |
| `storage:journald` | `ext4`         |        1GiB | `/var/log/journal`  |
| `storage:home`     | `ext4`         |      256MiB | `/home`             |
| `storage:swap`     | `swap`         |        1GiB | `swap`              |

{{< alert "sensor-triangle-exclamation" >}} Monitoring for the available
capacity on the `/var/log/journal` and `/var/cache/pacman` partitions will
normally be disabled as they will operate at close to full in typical operation.
{{< /alert >}}

## Vault Cluster

| Partition       | Type   | Size | Becomes          |
| :-------------- | :----- | ---: | :--------------- |
| `storage:vault` | `ext4` | 2GiB | `/var/lib/vault` |

## Consul Cluster

| Partition        | Type   | Size | Becomes           |
| :--------------- | :----- | ---: | :---------------- |
| `storage:consul` | `ext4` | 2GiB | `/var/lib/consul` |

## etcd Cluster

| Partition      | Type   | Size | Becomes         |
| :------------- | :----- | ---: | :-------------- |
| `storage:etcd` | `ext4` | 2GiB | `/var/lib/etcd` |

## Netdata Parent

| Partition         | Type   |   Size | Becomes            |
| :---------------- | :----- | -----: | :----------------- |
| `storage:netdata` | `ext4` | 128GiB | `/var/lib/netdata` |

## Kubernetes Controller

| Partition            | Type   |  Size | Becomes               |
| :------------------- | :----- | ----: | :-------------------- |
| `storage:containerd` | `ext4` | 32GiB | `/var/lib/containerd` |

## Kubernetes Workers

| Partition            | Type   |  Size | Becomes               |
| :------------------- | :----- | ----: | :-------------------- |
| `storage:containerd` | `ext4` | 96GiB | `/var/lib/containerd` |

## Kubernetes Storage

| Partition            | Type   |  Size | Becomes               |
| :------------------- | :----- | ----: | :-------------------- |
| `storage:containerd` | `ext4` | 32GiB | `/var/lib/containerd` |
| `/dev/vdb1`          | LVM    |  1TiB | `lvm:state`           |
| `state:kubernetes`   | (none) |  1TiB | Ceph Block Storage    |
