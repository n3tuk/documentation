---
title: Starting the Build
date: 2023-07-18T23:16:12+01:00
featureAlt: "Coffee cup with spoon and sugar cubes in the layout of a clock on a red background. Photo by Stas Knop."
# description: Automatically generated.
# summary: Automatically generated.
tags:
  - post
  - hardware
  - building
---

Welcome to my first blog in many years, but also an overall opportunity to
restart the development of my personal and development infrastructure at home.
Something I have been wanting to do for many years, and now I'm finally making
it a reality.

The overall plan is:

- Development of multiple GitHub Actions to facilitate management of GitHub
  repositories, including [handling pull requests][action-pull-requester], and
  [syncing upstream template repositories][action-synchronise-upstream] with
  downstream instances, and potentially [one][action-terraform-docs] for
  improved [`terraform-docs`][terraform-docs] handling too.
- Creation of [Terraform][terraform] modules for the management of initial
  resources, including GitHub repositories, Terraform
  [modules][template-terraform-modules] themselves, and Terraform
  [configurations][template-terraform-configuration] through workspaces in
  [Terraform Cloud][terraform-cloud].
- Build out a cluster of Intel NUC i5-1340p nodes with 64GiB RAM and 2TB SSDs.
- Build out highly available Vault and Consul clusters for supporting a
  Certificate Authority, secrets management, central configuration, and service
  mesh.
- Build out an [etcd][etcd] cluster for Kubernetes.
- Build out a Kubernetes Cluster with 3 controllers, 15 worker nodes and 5
  storage notes.
- Configure [Calico][calico] as the CNI.
- Configure [Ceph][ceph] as the CSI.
- Configure [Vault][vault] as the [Secrets Store CSI][secrets-store-csi].
- Configure the Kubernetes cluster using [Flux][flux].
- Integrate tools such as Metal LB, Cloudflare Argo Tunnels, Vault, Consul,
  ingress-nginx, external-dns, cert-manager, Ceph, NetData, loki, Prometheus,
  Grafana, and more.
- Deploy services such as PostgreSQL, Concourse, GitHub Actions Operator,
  Terraform Cloud Operator, as well as local testing applications.

{{< alert >}} I expect this to take most of the rest of 2023 to complete, with
some aspects using Ansible (such as the physical hosts and virtual machines) and
others using combinations of Terraform, Flux, Concourse, and GitHub Actions.
{{< /alert >}}

[action-pull-requester]: https://github.com/n3tuk/action-pull-requester
[action-synchronise-upstream]:
  https://github.com/n3tuk/action-synchronise-upstream
[action-terraform-docs]: https://github.com/n3tuk/action-terraform-docs
[terraform]: https://www.terraform.io
[terraform-cloud]: https://app.terraform.io
[terraform-docs]: https://terraform-docs.io
[template-terraform-modules]: https://github.com/n3tuk/template-terraform-module
[template-terraform-configuration]:
  https://github.com/n3tuk/template-terraform-configuration
[etcd]: https://etcd.io
[calico]: https://github.com/projectcalico/calico
[ceph]: https://ceph.io/en/
[vault]: https://www.vaultproject.io
[flux]: https://fluxcd.io
[secrets-store-csi]: https://github.com/kubernetes-sigs/secrets-store-csi-driver
