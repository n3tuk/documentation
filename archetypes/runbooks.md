---
title: {{replace .Name "-" " " | title}}
date: {{.Date}}
description: |-
  Runbook for the general maintenance and management of {{replace .Name "-" " "}}.
summary: |-
  Runbook for the general maintenance and management of {{replace .Name "-" " "}}.
tags:
  - runbook
  - {{.Name | lower}}
draft: true
---

## Service Overview

{{< alert "edit" >}} Provide an overview of the
{{ replace .Name "-" " " | title }} service; noting it's general setup and
purpose. {{< /alert >}}

## Service Details

| Detail     | Value   |
| :--------- | :------ |
| URI        | `{uri}` |
| Repository | `{uri}` |
| Monitoring | `{uri}` |
| Logs       | `{uri}` |
| Alerting   | `{uri}` |

### Upstream Dependencies

This service is currently dependent upon the following services and/or systems
for normal operation, and for what reasons that dependency is needed.

| Service |                     Importance                      | Description   |
| :------ | :-------------------------------------------------: | :------------ |
| {name}  | {{< badge >}}critical,high,medium,low{{< /badge >}} | {description} |

### Downstream Dependencies

This service is currently dependent upon **by** following services and/or
systems for normal operation, and for what reasons that dependency is needed.

| Service |                     Importance                      | Description   |
| :------ | :-------------------------------------------------: | :------------ |
| {name}  | {{< badge >}}critical,high,medium,low{{< /badge >}} | {description} |

## Maintenance Tasks

{{< alert "edit" >}} Provide information about the maintenance tasks which may
be needed for this service or system, such as rotation of credentials or
certificates, reclaiming disk space, or other operational tasks. {{< /alert >}}

### Name of Task

Provide a description of the task which needs to be documented, providing both
the context for the task, as well as the expected output for resolution.

- Note the steps which can be used to check if/when this task needs to be
  performed or updated.

1. Note the steps which are needed in order to run this task.
1. Note the steps which are needed to check that the task has completed
   successfully too.

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
