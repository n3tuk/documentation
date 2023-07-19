---
title: Certificate Authority with Hashicorp Vault
date: 2023-07-19T13:50:30+01:00
description: |-
  Runbook for the maintenance and management of the intermediate Certificate
  Authority service within the Hashicorp Vault Cluster for the n3tuk
  Organisation.
summary: |-
  Runbook for the maintenance and management of intermediate Certificate
  Authority service within the Hashicorp Vault Cluster for the `n3tuk`
  Organisation, providing automated certificates trusted through the root
  certificate of the Organisation.
tags:
  - runbook
  - hashicorp
  - vault
  - certificate-authority
draft: true
---
## Service Overview

{{< alert "edit" >}}
Provide an overview of the Hashicorp Vault service; noting it's general setup
and purpose.
{{< /alert >}}

## Service Details

| Detail | Value |
| :--- | :--- |
| URI | `{uri}` |
| Repository | `{uri}` |
| Monitoring | `{uri}` |
| Logs | `{uri}` |
| Alerting | `{uri}` |

### Upstream Dependencies

This service is currently dependent upon the following services and/or systems
for normal operation, and for what reasons that dependency is needed.

| Service | Importance | Description |
| :---| :---: | :--- |
| {name} | {critical,high,medium,low} | {description} |

### Downstream Dependencies

This service is currently dependent upon **by** following services and/or systems
for normal operation, and for what reasons that dependency is needed.

| Service | Importance | Description |
| :---| :---: | :--- |
| {name} | {critical,high,medium,low} | {description} |

## Maintenance Tasks

{{< alert "edit" >}}
Provide information about the maintenance tasks which may be needed for this
service or system, such as rotation of credentials or certificates, reclaiming
disk space, or other operational tasks.
{{< /alert >}}

### Name of Task

Provide a description of the task which needs to be documented, providing both
the context for the task, as well as the expected output for resolution.

* Note the steps which can be used to check if/when this task needs to be
  performed or updated.

1. Note the steps which are needed in order to run this task.
1. Note the steps which are needed to check that the task has completed
   successfully too.

## Incidents

{{< alert "edit" >}}
Provide information about potential incidents which may occur within the service
or system, such as a crash, loss of selected services, or a denial of service.
{{< /alert >}}

### Name of Incident

Provide a description of the incident which needs to be documented, providing both
the context for the error or failure, as well as the expected output for resolution.

* Note the steps which can be used to check if/when this incident is genuine and
  the following steps will need to be run.

1. Note the steps which are needed in order to resolve this incident.
1. Note the steps which are needed to check that the incident has been resolved
   and the service or system is running successfully too.
