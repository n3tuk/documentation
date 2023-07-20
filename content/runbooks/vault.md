---
title: Hasicorp Vault
date: 2023-07-19T13:50:15+01:00
description: |-
  Runbook for the general maintenance and management of the Hashicorp Vault
  Cluster for the n3tuk Organisation.
summary: |-
  Runbook for the general maintenance and management of the Hashicorp Vault
  Cluster for the `n3tuk` Organisation.
tags:
  - runbook
  - clusters
  - hashicorp
  - vault
  - secrets
  - certificate-authority
  - oidc
  - authentication
  - authorisation
draft: true
---

## Service Overview

Hashicorp Vault is a tool for A tool for secrets management, encryption as a
service, and privileged access management. Within the `n3tuk` Organisation,
Vault provides:

1. Storage and rotation of secrets with controlled access based on
   locally-managed policies, both directly via the service and via the Secrets
   Store CSI in Kubernetes;
1. Dynamic generation of credentials for access to third-party providers (such
   as AWS or Databases), both with standard and custom policies for access;
1. OIDC provider and client authentication, allowing users to authenticate in
   local services, and local services to authenticate against Vault for access
   by users.

## Service Details

| Detail     | Value                                                        |
| :--------- | :----------------------------------------------------------- |
| URI        | [`https://vault.kub3.uk`][kub3-vault]                        |
| Repository | [`https://github.com/n3tuk/infra-vault`][github-infra-vault] |
| Monitoring | `n/a`                                                        |
| Logs       | `n/a`                                                        |
| Alerting   | `n/a`                                                        |

[kub3-vault]: https://vault.kub3.uk
[github-infra-vault]: https://github.com/n3tuk/infra-vault

### Upstream Dependencies

This service is currently dependent upon the following services and/or systems
for normal operation, and for what reasons that dependency is needed.

| Service                 |                         Importance                         | Description                                                                                                                                                                                           |
| :---------------------- | :--------------------------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cloudflare Argo Tunnels | {{< state "triangle-exclamation" >}}critical{{< /state >}} | Cloudflare Argo Tunnels provide the external access to Vault and facilitate the security of the service. Failure of this service will result in failure to access Vault outside of the local network. |

### Downstream Dependencies

This service is currently dependent upon **by** following services and/or
systems for normal operation, and for what reasons that dependency is needed.

| Service |                         Importance                         | Description                                                                                                                                                                 |
| :------ | :--------------------------------------------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| (~All)  | {{< state "triangle-exclamation" >}}critical{{< /state >}} | Vault is a core component of many parts of the `ntkuk` Organisation infrastructure, supporting service secrets and OIDC to facilitate internal and external authentication. |

## Maintenance Tasks

{{< alert "fish" >}} All commands provided here assume the use of the `fish`
shell, not `bash`. If you are using `bash`, please check and convert them where
required. {{< /alert >}}

### Unseal on Upgrade/Restart

Vault is configured with a local store that is encrypted at rest on each of the
nodes of the cluster (even though those drives are encrypted at rest too). The
key to this local store is not saved with Vault and as such when each of the
nodes in the cluster restarts it must be provided with the unseal key to unlock
the store and start processing requests.

- In order to check if the Vault cluster is sealed and therefore cannot serve
  requests, run the following command:

  ```console {linenos=false,hl_lines=[1]}
  $ vault status -format=json | jq -r '.sealed'
  true # The vault is sealed and needs unsealing
  ```

1. Run the `unseal` command with `vault` using the decrypted unseal key:

   ```fish
   vault operator unseal (
     pass show n3tuk/clusters/vault/keys/unseal \
       | base64 -d \
       | gpg -d
   )
   ```

1. Re-run the above check to verify that the result is `true`.

### Rekey the Unseal Key

{{< alert "edit" >}} This task is currently just a placeholder; it has yet to be
written.<br />Please wait for it to be updated. {{< /alert >}}

Vault is managed with a local store which is encrypted at rest on each of the
nodes of the cluster (even though those drives are encrypted at rest too). The
key to this local store is not saved with Vault and as such when the cluster
restarts it must be provided with the that key to start operations.

- Note

1. Note

### Rotate the Encryption Key

{{< alert "edit" >}} This task is currently just a placeholder; it has yet to be
written.<br />Please wait for it to be updated. {{< /alert >}}

Vault is managed with a local store which is encrypted at rest on each of the
nodes of the cluster (even though those drives are encrypted at rest too). The
key to this local store is not saved with Vault and as such when the cluster
restarts it must be provided with the that key to start operations.

- Note

1. Note

### Rotating the Intermediate Certificate Authority

{{< alert "edit" >}} This task is currently just a placeholder; it has yet to be
written.<br />Please wait for it to be updated. {{< /alert >}}

Vault is managed with a local store which is encrypted at rest on each of the
nodes of the cluster (even though those drives are encrypted at rest too). The
key to this local store is not saved with Vault and as such when the cluster
restarts it must be provided with the that key to start operations.

- Note

1. Note
