---
title: Mikrotik Routers and Switches
date: 2023-11-01T00:00:00+00:00
description: |-
  The runbook for managing Mikrotik devices, including the routers and switches
  which provide the networking functions.
summary: |-
  The runbook for managing Mikrotik devices, including the routers and switches
  which provide the backbone of the `n3tuk` Lab environment.
tags:
  - runbook
  - mikrotik
  - routeros
draft: true
---

## Service Overview

{{< alert "edit" >}} Provide an overview of the virtual machines; noting it's
general setup and purpose. {{< /alert >}}

## Service Details

| Detail     | Value                                        |
| :--------- | :------------------------------------------- |
| URI        | `n/a`                                        |
| Repository | [`n3tuk/scripts-mikrotik`][scripts-mikrotik] |
| Monitoring | `n/a`                                        |
| Logs       | `n/a`                                        |
| Alerting   | `n/a`                                        |

[scripts-mikrotik]: https://github.com/n3tuk/scripts-mikrotik

### Upstream Dependencies

This service is currently dependent upon the following services and/or systems
for normal operation, and for what reasons that dependency is needed.

| Service |                      Importance                       | Description                                                     |
| :------ | :---------------------------------------------------: | :-------------------------------------------------------------- |
| (none)  | {{< state "triangle-exclamation" >}}low{{< /state >}} | There are no upstream dependencies on this system at this time. |

### Downstream Dependencies

This service is currently dependent upon **by** following services and/or
systems for normal operation, and for what reasons that dependency is needed.

| Service |                         Importance                         | Description                                                                                                                                                                   |
| :------ | :--------------------------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| (~All)  | {{< state "triangle-exclamation" >}}critical{{< /state >}} | The network is a core component of the `n3tuk` Organisation infrastructure, providing connectivity between all systems and services, as permitted, and Internet connectivity. |

## Maintenance Tasks

{{< alert "fish" >}} All commands provided here assume the use of the `fish`
shell, not `bash`. If you are using `bash`, please check and convert them where
required. {{< /alert >}}

### Certificate Updates

All Mikrotik hosts provide a local web service which runs WebFig for remote
management, as well as access to graphs and API. As with all certificates, they
must be regulary updated as and when they expire.

- To perform a check on the certificate of a Mikrotik host, run the following
  command which will verify if the presented certificate will expire within the
  next four weeks:

  ```console
  $ echo -n Q \
    | openssl s_client \
        -connect {hostname}:443 \
      2>/dev/null \
    | openssl x509 -noout -enddate -checkend 604800
  notAfter=Jan 1 00:00:00 2099 GMT
  Certificate will not expire
  ```

1. Fetch the `n3tuk-ca.img` file to the local system in order to mount it to
   access its contents.

1. Using `losetup` and `cryptsetup` to attach the file to the system and open it
   for mounting, with `pass` providing the required passphrase to open the
   encrypted storage:

   ```console
   $ pass show keys/certificate-authority/crypt
   {passphrase}
   $ sudo losetup --find n3tuk-ca.img
   $ sudo cryptsetup open /dev/loop0 certificate-authority
   Enter passphrase for n3tuk-ca.img: {passphrase}
   $ mount /dev/mapper/certificate-authority -o noatime /mnt/tmp
   ```

## Incidents

{{< alert "edit" >}} Provide information about potential incidents which may
occur within the service or system, such as a crash, loss of selected services,
or a denial of service. {{< /alert >}}

### Name of Incident

Provide a description of the incident which needs to be documented, providing
both the context for the error or failure, as well as the expected output for
resolution.

- Note the steps which can be used to check if/when this incident is genuine and
  the following steps will need to be run.

1. Note the steps which are needed in order to resolve this incident.
1. Note the steps which are needed to check that the incident has been resolved
   and the service or system is running successfully too.
