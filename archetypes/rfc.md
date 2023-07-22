---
title: RFC-{{replace .Name "-" " " | title}}
date: {{.Date}}
description: |-
  RFC document discussuing {{delimit (after 1 (split .Name "-")) " " }}.
summary: |-
  RFC document discussuing {{delimit (after 1 (split .Name "-")) " " }}.
tags:
  - rfc
  - rfc-{{delimit (first 1 (split .Name "-")) ""}}
  - {{delimit (after 1 (split .Name "-")) "-"}}
draft: true
showEdit: true
---

## Summary

{{< alert "edit" >}} Provide a summary of this feature or change in one
paragraph to provide context for the reader. {{< /alert >}}

## Motivation

{{< alert "edit" >}} What is the reason behind this feature or change and what
will it provide? How will we know this is successful? {{< /alert >}}

## Design

{{< alert "edit" >}} Provide the design for this feature or change which gives
an insight to all the components, data flows, configurations, and dependencies.
{{< /alert >}}

## Discussion

{{< alert "edit" >}} Provide a discussion of the design and implementation to
give all the relevant information needed to understand this RFC. {{< /alert >}}

## Opportunities

{{< alert "edit" >}} What are the opportunities that this feature or change will
afford? {{< /alert >}}

## Obstacles

{{< alert "edit" >}} What are the obstacles in the way to either the
implementation of this feature or change, or it's long-term operation?
{{< /alert >}}

## Alternatives

{{< alert "edit" >}} What are the alternatives that were considered for this
feature or change, but where rejected? {{< /alert >}}

## Rationale

{{< alert "edit" >}} What is the rationale for proceeding with this change or
feature? {{< /alert >}}

## Open Questions

{{< alert "edit" >}} What open questions have yet to be answered by this
document, but should be considered during implementation? {{< /alert >}}

## Future Possibilities

{{< alert "edit" >}} What are the next steps that may be considered following
the implementation of this feature or change, but which should not be handled
during? {{< /alert >}}
