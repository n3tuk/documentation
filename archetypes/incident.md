---
title: {{first 1 (split .Date "T")}} {{replace .Name "-" " " | title}}
date: {{.Date}}
description: |-
  An incident document discussuing {{replace .Name "-" " " | title}}.
summary: |-
  An incident document discussuing {{replace .Name "-" " " | title}}.
tags:
  - incident
draft: true
showEdit: false
---

## Summary

{{< alert "edit" >}} Provide a summary of this incident in one paragraph to
provide context for the reader. {{< /alert >}}

## Timeline

{{< alert "edit" >}} What is the reason behind this feature or change and what
will it provide? How will we know this is successful? {{< /alert >}}

| Time  | Event | Owner     | Description                     |
| :---: | :---- | :-------- | :------------------------------ |
| 00:00 | Alert | @jonathan | Alerted to the incident by xyz. |

## Cause

{{< alert "edit" >}} What is the identified root cause in the chain of events
for this incident? Use the Five Whys. {{< /alert >}}

The Five Whys is a root cause identification technique: it:

- Begin with a description of the impact and ask why it occurred.
- Note the impact that it had.
- Ask why this happened, and why it had the resulting impact.
- Then, continue asking “why” until you arrive at a root cause.

## Impact

{{< alert "edit" >}} What is the impact of this incident? {{< /alert >}}

Be very specific here and include exact numbers.

| Impact                        | Numbers                                             |
| :---------------------------- | :-------------------------------------------------- |
| Events Dropped / Not Accepted | 0% (0 of 0) (Should usually be 0, but always check) |
| Accounts Affected             | 0                                                   |
| Users Affected                | 0                                                   |
| Support Requests Raised       | 0 (Include any relevant links to tickets)           |

## Detection

{{< alert "edit" >}} How was this incident detected? {{< /alert >}}

## Contributors

{{< alert "edit" >}} Are there any events or situations which contributed to
this incident? {{< /alert >}}

## Mitigators

{{< alert "edit" >}} Are there any events or situations which should be called
out as helping to mitigate the incident? {{< /alert >}}

## Recovery

{{< alert "edit" >}} What were the steps involved in recovery from this
incident? {{< /alert >}}

## Prevention

{{< alert "edit" >}} Are there any tasks or changes which has been identified
though this analysis which can prevent or mitigate the risks associated with
this incident? {{< /alert >}}

## Links & References

{{< alert "edit" >}} Add any links to tickets or pages which may be useful to
the context of this incident, or reference any follow-up. {{< /alert >}}
