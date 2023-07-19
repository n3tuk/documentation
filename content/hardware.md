---
title: Hardware Overview
date: 2023-07-18T22:00:00+01:00
description: |-
  An overview of the hardware used by the n3tuk infrastructure
summary: |-
  An overview of the hardware used to connect and host the `n3tuk` infrastructure,
  including the kubernetes cluster, storage, and networking, alongside the
  general configuration and how they're managed.
tags:
  - hardware
---

## Intel NUC Servers

|        Component | Description                                                        | Qty |
| ---------------: | ------------------------------------------------------------------ | :-: |
|           System | [Intel NUC13ANKi5][intel-nuc]                                      |  1  |
|              CPU | [Intel Core i5-1340p][intel-i5-1340p] 4.60GHz (4+4/8)              |  1  |
|              RAM | [Corsair Vengeance 32GiB][corsair-64gb] (DDR4-3200) Non-ECC SODIMM |  2  |
|            Drive | [Samsung 990 Pro 2TB][samsung-990pro-2tb] PCIe 4.0 NVMe M.2 SSD    |  1  |
|          Network | Intel i226-V 2.5GbE Port                                           |  1  |
| Operating System | Arch Linux (64-bit)                                                |  -  |

[intel-nuc]: https://www.intel.com/content/www/us/en/products/sku/233098/intel-nuc-13-pro-kit-nuc13anhi5/specifications.html
[intel-i5-1340p]: https://www.intel.com/content/www/us/en/products/sku/232126/intel-core-i51340p-processor-12m-cache-up-to-4-60-ghz/specifications.html
[corsair-64gb]: https://www.corsair.com/uk/en/p/memory/cmsx64gx4m2a3200c22/vengeancea-series-64gb-2-x-32gb-ddr4-sodimm-3200mhz-cl22-memory-kit-cmsx64gx4m2a3200c22
[samsung-990pro-2tb]: https://www.samsung.com/uk/memory-storage/nvme-ssd/990-pro-2tb-nvme-pcie-gen-4-mz-v9p2t0bw/

|         Partition | Type | Size     | Description                                                                                                                            |
| ----------------: | ---- | -------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `/dev/nvmen0n1p1` | ESP  | ~1GiB    | ESP partition for UEFI booting using signed UKI Kernels                                                                                |
| `/dev/nvmen0n1p2` | LVM  | ~1682GiB | LVM to provide `system`, `home`, and `swap` partitions for the underlying hypervisor, and all other block devices for virtual machines |

## Network-Attached Storage

- 1x [Synology DS1821+][synology-ds1821] 8-bay Network Attached Storage, with:
  - 2x [Seagate Exos X16 16TB][seagate-x16-16tb] Hard Drives (RAID1);
  - 6x [Seagate IronWolf 8TB][seagate-ironwolf-8tb] Hard Drives (RAID6);
  - 2x [Seagate FireCuda 1TB][seagate-firecuda-1tb] Solid State Drives for
    Read/Write Cache (RAID1); and
  - 1x [10Gtek X520-10G-1S-8X][10gtek-x520] 10GbE PCIe Network Card (Intel 82599EN),

[synology-ds1821]: https://www.synology.com/en-uk/products/DS1821+
[seagate-x16-16tb]: https://www.seagate.com/files/www-content/datasheets/pdfs/exos-x16-DS2011-1-1904US-en_US.pdf
[seagate-ironwolf-8tb]: https://www.seagate.com/www-content/datasheets/pdfs/ironwolf-12tb-emea-DS1904-20-2111GB-en_GB.pdf
[seagate-firecuda-1tb]: https://www.seagate.com/www-content/datasheets/pdfs/firecuda-510-ssd-DS1999-1-1812US-en_US.pdf
[10gtek-x520]: https://www.10gtek.com/10gnic

## Network Switches & Router

- 1x [MiktoTik CRS309 CRS309-1G-8S+IN][mikrotik-crs509] Switch.
- 1x [MikroTik RB4011iGS+RM][mikrotik-rb4011] Router.
- 1x [Eaton 5S1500I][eaton-5s1500i] UPS.

[mikrotik-crs509]: https://mikrotik.com/product/crs309_1g_8s_in
[mikrotik-rb4011]: https://mikrotik.com/product/rb4011igs_rm
[eaton-5s1500i]: https://www.eaton.com/gb/en-gb/skuPage.5S1500I.html

## Network

|  VLAN |              IPv4 | Nodes                  |                         Range | Description                                                                 |
| ----: | ----------------: | :--------------------- | ----------------------------: | :-------------------------------------------------------------------------- |
| `300` |   `172.27.6.0/26` | 5 NUC Hosts            |    `172.27.6.0 - 172.27.6.63` | Physical Server VLAN                                                        |
| `301` |  `172.27.6.64/27` | (No Hosts Configured)  |   `172.27.6.64 - 172.27.6.95` | Physical Server VLAN                                                        |
| `302` |  `172.27.6.96/27` | upto 32 Load Balancers |  `172.27.6.96 - 172.27.6.127` | MetalLB Addresses (BGP via VLAN `302`)                                      |
| `302` | `172.27.6.128/25` | 45 Virtual Machines    | `172.27.6.128 - 172.27.6.255` | Service Cluster and Kubernetes Virtual Machines (Routable)                  |
| `303` | `172.27.7.128/25` | Virtual Machines & NAS | `172.27.7.128 - 172.27.7.255` | Virtual Machines (Non-Routable) and Network Attached Storage                |
|   n/a |   `172.27.8.0/21` | upto 2,048 Services    |  `172.27.8.0 - 172.27.15.255` | Service Addresses (One IPv4 address per service, non-routable outside VLAN) |
|   n/a |  `172.27.16.0/20` | 16 Nodes               | `172.27.16.0 - 172.27.31.255` | Pod Addresses (One IPv4 `/24` per node, non-routable outside VLAN)          |

Note: The initial configuration for the `metallb` service should just use a
subset of the Virtual Machine IP address allocation so that it can just allocate
an unused, but routable IP address. The second stage will be to migrate it to a
fully owned subnet allocation which can be managed with BGP back to the router,
which will direct traffic to the correct node

## Domains

|      Name | Description |
| --------: | :---------- |
| `kub3.uk` | n/a         |
| `sit3.uk` | n/a         |
| `t3st.uk` | n/a         |
| `pip3.uk` | n/a         |
| `liv3.uk` | n/a         |
