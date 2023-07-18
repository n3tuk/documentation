---
title: Hardware
weight: 1
bookToc: true
---
# `n3tuk` Hardware Overview

There are five main components to the hardware for the `n3tuk` infrastructure:

* 5x [Intel NUC13ANKi5 i5-1340p][intel-nuc], with:
  * Corsair 64GiB DDR4 SODIMM CL22 3200MT/s Memory (2x32GiB);
  * Samsung 990 Pro 2TB NVMe SSD; plus
  * Intel Ethernet i226-V, connected at 2.5GbE to:
* 1x [Synology DS1821+][synology-ds1821] 8-bay Network Attached Storage, with:
  * 2x [Seagate Exos X16 16TB][seagate-x16-16tb] Hard Drives (RAID1);
  * 6x [Seagate IronWolf 8TB][seagate-ironwolf-8tb] Hard Drives (RAID6);
  * 2x [Seagate FireCuda 1TB][seagate-firecuda-1tb] Solid State Drives for
    Read/Write Cache (RAID1); and
  * 1x [10Gtek X520-10G-1S-8X][10gtek-x520] 10GbE PCIe Network Card (Intel 82599EN),
* 1x [MiktoTik CRS309 CRS309-1G-8S+IN][mikrotik-crs509] Switch.
* 1x [MikroTik RB4011iGS+RM][mikrotik-rb4011] Router.
* 1x [Eaton 5S1500I][eaton-5s1500i] UPS.

[intel-nuc]: https://www.intel.com/content/www/us/en/products/sku/233098/intel-nuc-13-pro-kit-nuc13anhi5/specifications.html
[synology-ds1821]: https://www.synology.com/en-uk/products/DS1821+
[seagate-x16-16tb]: https://www.seagate.com/files/www-content/datasheets/pdfs/exos-x16-DS2011-1-1904US-en_US.pdf
[seagate-ironwolf-8tb]: https://www.seagate.com/www-content/datasheets/pdfs/ironwolf-12tb-emea-DS1904-20-2111GB-en_GB.pdf
[seagate-firecuda-1tb]: https://www.seagate.com/www-content/datasheets/pdfs/firecuda-510-ssd-DS1999-1-1812US-en_US.pdf
[10gtek-x520]: https://www.10gtek.com/10gnic
[mikrotik-crs509]: https://mikrotik.com/product/crs309_1g_8s_in
[mikrotik-rb4011]: https://mikrotik.com/product/rb4011igs_rm
[eaton-5s1500i]: https://www.eaton.com/gb/en-gb/skuPage.5S1500I.html
