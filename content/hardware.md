---
title: Hardware Overview
date: 2023-07-18T22:00:00+01:00
description: |-
  An overview of the hardware used by the n3tuk infrastructure.
summary: |-
  An overview of the hardware used to connect and host the `n3tuk` infrastructure,
  including the kubernetes cluster, storage, and networking, alongside the
  general configuration and how they're managed.
tags:
  - hardware
showEdit: false
---

## Intel NUC Servers

|        Component | Description                                                        | Qty |
| ---------------: | ------------------------------------------------------------------ | :-: |
|           System | [Intel NUC13ANKi5][intel-nuc]                                      |  1  |
|              CPU | [Intel Core i5-1340p][intel-i5-1340p] 4.60GHz (4+4/8)              |  1  |
|              RAM | [Corsair Vengeance 32GiB][corsair-64gb] (DDR4-3200) Non-ECC SODIMM |  2  |
|            Drive | [Seagate FireCuda 530 2TB][firecuda-530-2tb] PCIe 4.0 NVMe M.2 SSD |  1  |
|          Network | Intel i226-V 2.5GbE Port                                           |  1  |
| Operating System | Arch Linux (64-bit)                                                |  -  |

[intel-nuc]:
  https://www.intel.com/content/www/us/en/products/sku/233098/intel-nuc-13-pro-kit-nuc13anhi5/specifications.html
[intel-i5-1340p]:
  https://www.intel.com/content/www/us/en/products/sku/232126/intel-core-i51340p-processor-12m-cache-up-to-4-60-ghz/specifications.html
[corsair-64gb]:
  https://www.corsair.com/uk/en/p/memory/cmsx64gx4m2a3200c22/vengeancea-series-64gb-2-x-32gb-ddr4-sodimm-3200mhz-cl22-memory-kit-cmsx64gx4m2a3200c22
[firecuda-530-2tb]:
  https://www.seagate.com/gb/en/products/gaming-drives/pc-gaming/firecuda-530-ssd/

|         Partition | Type | Size     | Description                                                                                                                            |
| ----------------: | ---- | -------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `/dev/nvmen0n1p1` | ESP  | ~1GiB    | ESP partition for UEFI booting using signed UKI Kernels                                                                                |
| `/dev/nvmen0n1p2` | LVM  | ~1682GiB | LVM to provide `system`, `home`, and `swap` partitions for the underlying hypervisor, and all other block devices for virtual machines |

## Network-Attached Storage

|        Component | Description                                                                  | Qty |
| ---------------: | ---------------------------------------------------------------------------- | :-: |
|           System | [Synology DS1821+][synology-ds1821] 8-bay NAS                                |  1  |
|              RAM | [NEMIX 16GiB][nemix-16gb] (DDR4-2666) EEC SODIMM                             |  2  |
|   Drives (Media) | [Seagate Exos X16 16TB][seagate-x16-16tb] Hard Drives (RAID1)                |  3  |
|   Drives (iSCSI) | [Seagate IronWolf 8TB][seagate-ironwolf-8tb] Hard Drives (RAID6)             |  7  |
|   Drives (Cache) | [Seagate FireCuda 510 1TB][seagate-firecuda-1tb] Solid State Drives (RAID1)  |  2  |
|          Network | [10Gtek X520-10G-1S-8X][10gtek-x520] 10GbE PCIe Network Card (Intel 82599EN) |  1  |
| Operating System | Synology DMS 7.2                                                             |  -  |

{{< alert "disc-drive" >}} The _iSCSI_ drives were purchased in two sets of four
and three, approximately three years apart and the fourth of the first set is
available as an on-site spare. The _Media_ drives have an on-site spare as well.
{{< /alert >}}

[synology-ds1821]: https://www.synology.com/en-uk/products/DS1821+
[nemix-16gb]:
  https://nemixram.com/products/ddr4-2666mhz-pc4-21300-ecc-sodimm-compatible-with-synology-diskstation-ds1821?variant=45120171671864
[seagate-x16-16tb]:
  https://www.seagate.com/files/www-content/datasheets/pdfs/exos-x16-DS2011-1-1904US-en_US.pdf
[seagate-ironwolf-8tb]:
  https://www.seagate.com/www-content/datasheets/pdfs/ironwolf-12tb-emea-DS1904-20-2111GB-en_GB.pdf
[seagate-firecuda-1tb]:
  https://www.seagate.com/www-content/datasheets/pdfs/firecuda-510-ssd-DS1999-1-1812US-en_US.pdf
[10gtek-x520]: https://www.10gtek.com/10gnic

## Network Switches & Router

|       Component | Description                                        | Qty |
| --------------: | -------------------------------------------------- | :-: |
|          Router | [MikroTik RB4011iGS+RM][mikrotik-rb4011]           |  1  |
| Backbone Switch | [Mikrotik CRS328-24P-4S+RM][mikrotik-crs328]       |  1  |
|  Cluster Switch | [MiktoTik CRS309 CRS309-1G-8S+IN][mikrotik-crs309] |  1  |

[mikrotik-crs309]: https://mikrotik.com/product/crs309_1g_8s_in
[mikrotik-crs328]: https://mikrotik.com/product/crs328_24p_4s_rm
[mikrotik-rb4011]: https://mikrotik.com/product/rb4011igs_rm

## UPS Power

| Component | Description                        | Qty |
| --------: | ---------------------------------- | :-: |
|       UPS | [Eaton 5S1500I][eaton-5s1500i] UPS |  1  |

{{< alert "power-off" >}} The battery currently provides approximately 55
minutes of runtime in the event of either a power outage or brownout, and is
connected via USB to a host using Network USB Tools to distribute events.
{{< /alert >}}

[eaton-5s1500i]: https://www.eaton.com/gb/en-gb/skuPage.5S1500I.html
