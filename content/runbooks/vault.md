---
title: Hasicorp Vault
date: 2023-11-01T12:00:00+00:00
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

| Detail     | Value                                                                                                                                                                                   |
| :--------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| URI        | [`https://vault.p.kub3.uk`][kub3-prd-vault] (Production)<br />[`https://vault.d.kub3.uk`][kub3-dev-vault] (Development)                                                                 |
| Repository | [`https://github.com/n3tuk/ansible`][github-ansible] (Virtual Machine & Service Installation)<br />[`https://github.com/n3tuk/infra-vault`][github-infra-vault] (Service Configuration) |
| Monitoring | `n/a`                                                                                                                                                                                   |
| Logs       | `n/a`                                                                                                                                                                                   |
| Alerting   | `n/a`                                                                                                                                                                                   |

[kub3-prd-vault]: https://vault.p.kub3.uk
[kub3-dev-vault]: https://vault.d.kub3.uk
[github-ansible]: https://github.com/n3tuk/ansible
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
| (~All)  | {{< state "triangle-exclamation" >}}critical{{< /state >}} | Vault is a core component of many parts of the `n3tuk` Organisation infrastructure, supporting service secrets and OIDC to facilitate internal and external authentication. |

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
       | gpg -d)
   ```

1. Re-run the above check to verify that the result is `true`.

### Rekey the Unseal Key

As `n3tuk` is purely a private system for Jonathan Wright, and no others are
involved, the unseal key for Vault is just a single value with only that one
value being required to unlock.

In the event that this changes, or during normal rotation of the unseal key,
then we must `rekey` the unseal key.

- There are no investigative steps or tests for this task.

1. Verify that there is no existing re-keying underway:

   ```console {linenos=false,hl_lines=[1]}
   $ vault operator rekey -status -format=json | jq '.started'
   false
   ```

   If there is a `rekey` started, then run the command to cancel it:

   ```fish
   vault operator rekey -cancel
   ```

1. Ensure that the `VAULT_TOKEN` has been escalated to the `root` token for the
   Vault cluster:

   ```fish
   set -e VAULT_TOKEN
   set -Ux VAULT_TOKEN (pass show n3tuk/clusters/vault/tokens/root)
   ```

1. Initiate the `rekey` sequence by configuring the key and the recipients:

   ```fish
   vault operator rekey \
     -init \
     -key-shares=1 -key-threshold=1 \
     -pgp-keys=keybase:jonathanio \
     -backup
   ```

1. Next, provide the unseal key, and as there is just a single key normally, the
   updated unseal key should be returned, so ensure it is saved back into the
   password store:

   ```fish
   vault operator rekey (
     pass show n3tuk/clusters/vault/keys/unseal \
       | base64 -d \
       | gpg -d) \
    | grep -E '^Key' \
    | awk '{print $3}' \
    | pass insert --echo --force n3tuk/clusters/vault/keys/unseal
   ```

1. If there is an issue with the `rekey` process and the original keys have not
   been deleted or destroyed, the original unseal keys in Vault can be restored
   by retrieving the backup:

   ```fish
   vault operator rekey \
     -backup-retrieve
   ```

   Otherwise they can be deleted:

   ```fish
   vault operator rekey \
     -backup-delete
   ```
