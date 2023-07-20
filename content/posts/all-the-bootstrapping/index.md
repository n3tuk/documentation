---
title: All the Bootstrapping
date: 2023-07-20T21:32:37+01:00
featureAlt: "The Lotus Temple in New Delhi, DL, India. Photo by Apostrophy Studio."
tags:
  - post
  - building
  - bootstrapping
draft: true
---

One of the biggest challenges right now during the building out all the
infrastructure is bootstrapping of the various components to a point at which
they are minimally operational and self-supporting. Things such as the GitHub
repositories for code, and managing templates and protections, to Terraform
Cloud for running the first Infrastructure as Code to deploy other resources, to
the physical servers which will run the different clusters and Kubernetes are
all in scope here.

## GitHub

To more effectively group the code and configuration, I've created a dedicated
Organisation in GitHub to host all the standard code, applications, and
credentials needed. This has been nice as there are more options available
compared just a standard user account. However, this needs lots of work.

I've been working on:

- A pair of GitHub Actions, with the first to manage checks on pull requests to
  look for anything missing, as well as a second for automating the
  synchronisation of changes from an upstream template repository with
  downstream implementations. Both of these include full integration workflows
  and validation testing.
- Template repositories for Terraform modules and configurations using
  [CookieCutter][cookiecutter] to both standardise their layouts, but facilitate
  future changes and improvements.
- Reusable GitHub Workflows which can provide standard integration patterns for
  different components, although right now this is focused on just Terraform
  (either as a configuration or a module).

[cookiecutter]: https://github.com/cookiecutter/cookiecutter

Once the template repositories are created they can be used to create the
Terraform modules for thing like new GitHub repositories, and modules and
configurations to deploy the resources for Terraform modules (i.e. repository
and the entry in Terraform Cloud Private Module Registry). From there the
bootstrapping resources can be imported.

I'm also working on a GitHub Action to facilitate the running of
[`terraform-docs`][terraform-docs] as part the integration of Terraform changes.
Unfortunately, the existing [GitHub Action][terraform-docs-gh] for
`terraform-docs` has stagnated in its development and this will hopefully
provide a good replacement option more generally.

[terraform-docs]: https://github.com/terraform-docs/terraform-docs
[terraform-docs-gh]: https://github.com/terraform-docs/gh-actions

## Terraform Cloud

Terraform Cloud will provide the initial facilitation of the deployment of the
Infrastructure as Code, especially the initial Terraform modules, configurations
to manage GitHub and Terraform Cloud. Also some of the initial general
resources, like [Cloudflare DNS][cloudflare-dns] and prepations for [Cloudflare
Tunnels][cloudflare-tunnels] in the early stages of this build.

Once the first configurations are built they can be imported and self-managed.

[cloudflare-dns]: https://www.cloudflare.com/dns/
[cloudflare-tunnels]: https://www.cloudflare.com/products/tunnel/

## Documentation

Documentation is an important part of any system and it's been good to work
again with Hugo to play with deploying static sites for maximum performance. The
last few days have been spent:

- Preparing some standard sections along with some placeholder documents which
  need to be started during the early stages of this build out.
- Work with [Cloudflare Pages][cloudflare-pages] to facilitate the build and
  deployment of the site though automation.

{{< alert "boot" >}} There is also a bootstrapping process too, being important
to gather the documentation and runbooks as components and services are built,
deployed, and configured.<br/>Once the above work on GitHub and Terraform Cloud
is completed, then the Infrastructure as Code for these services can be build
and imported {{< /alert >}}

This has been good to validate some themes and make a choice ([congo][congo]) as
well as make some customisations and local enhancements.

[cloudflare-pages]: https://pages.cloudflare.com/
[congo]: https://github.com/jpanther/congo
