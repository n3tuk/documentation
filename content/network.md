---
title: Network
date: 2023-07-18T22:00:00+01:00
description: |-
  An overview of the networking configuration for the n3tuk infrastruction.
summary: |-
  An overview of the networking configuration for the n3tuk infrastruction,
  including a look at the VLAN topology, firewall configurations, for both hosts
  and networks, as well as the VPNs.
tags:
  - network
draft: true
showEdit: false
---

{{< alert "edit" >}} This document is currently just a placeholder; it has yet
to be written.<br />Please wait for it to be updated. {{< /alert >}}

![System Overview](/diagrams/kub3-bridgend-01-vpc.png)

## Traffic Assumptions

| ID  | Protocol | Ports                                      | Note                                                                                                                                           |
| :-- | :------- | :----------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | IP       | `tcp/8201`                                 | All nodes within a Vault cluster will require inter-node communications on the cluster addresses using a dedicated port.                       |
| 2   | IP       | `tcp/443`                                  | All Consul nodes will require dedicated TLS certificates, issued by Vault, to provide trusted internal communications.                         |
| 3   | IP       | `tcp/8300`<br />`tcp/8301`<br />`tcp/8302` | All nodes within a Consul cluster will require inter-node communications on the cluster addresses using dedicated ports.                       |
| 4   | IP       | `tcp/443`                                  | All `etcd` nodes will require dedicated TLS certificates, issued by Vault, to provide trusted internal communications.                         |
| 5   | IP       | `tcp/2380`                                 | All notes with an `etcd` cluster will require inter-node communications on the cluster addresses using a dedicated port.                       |
| 6   | IP       | `tcp/2379`                                 | All Kubernetes Controller nodes will require access to `etcd` as a client to store configuration and locks for distirbuted tasks.              |
| 7   | IP       | `tcp/443`                                  | All Kubernetes Controller nodes will require dedicated TLS certificates, issued by Vault, to provide trusted internal communications.          |
| 8   | IP       | `tcp/6443`<br />`tcp/10250`                | All Kubernetes Worker and Storage nodes will require access to the Kubernetes API and Kubelet API                                              |
| 9   | VXLAN    | `n/a`                                      | All Storage nodes in the Kubernetes cluster will require inter-pod communications for the processing of data and replication of block devices. |
| 10  | VXLAN    | `n/a`                                      | All Worker nodes in the Kubernetes cluster will require the ability to request and mount Persistant Volumes within the cluster.                |
| 11  | IP       | `tcp/443`                                  | All Worker nodes in the Kubernetes cluster will require access to Vault to retrieve certificates, secrets, and dynamic or static credentails.  |
| 12  | IP       | `tcp/44`<br />`udp/8600`                   | All Worker nodes in the Kubernetes cluster will require access to Consul to retrieve configurations settings and service discovery.            |
| 13  | IP       | `tcp/3260`<br />`tcp/3263`<br />`tcp/3265` | All Worker nodes in the Kubernetes cluster will require access to the iSCSI service on the Synology Network Attached Storage.                  |

## Network Configuration

| Network                         | VLAN  | IPv4 CIDR        | IPv6 CIDR                  |
| :------------------------------ | :---: | :--------------- | :------------------------- |
| kub3-nat64                      | `30`  | `172.23.30.0/24` | `2a02:8010:8006:3a30::/64` |
| kub3-services                   | `31`  | `172.23.31.0/24` | `2a02:8010:8006:3a31::/64` |
| kub3-nodes                      | `32`  | `172.23.32.0/24` | `2a02:8010:8006:3a32::/64` |
| kub3-prod-bridgend-01a-admin    | `BGP` | `172.23.40.0/24` | `2a02:8010:8006:3a40::/64` |
| kub3-prod-bridgend-01a-worker   | `41`  | `172.23.41.0/24` | `2a02:8010:8006:3a41::/64` |
| kub3-prod-bridgend-01b-admin    | `42`  | `172.23.42.0/24` | `2a02:8010:8006:3a42::/64` |
| kub3-prod-bridgend-01b-worker   | `43`  | `172.23.43.0/24` | `2a02:8010:8006:3a43::/64` |
| kub3-prod-bridgend-01c-admin    | `44`  | `172.23.44.0/24` | `2a02:8010:8006:3a44::/64` |
| kub3-prod-bridgend-01c-worker   | `45`  | `172.23.45.0/24` | `2a02:8010:8006:3a45::/64` |
| kub3-prod-bridgend-01a-services | `40`  | `172.23.48.0/23` | `2a02:8010:8006:3a48::/64` |
| kub3-dev-bridgend-01a-admin     | `50`  | `172.23.50.0/24` | `2a02:8010:8006:3a50::/64` |
| kub3-dev-bridgend-01a-worker    | `51`  | `172.23.51.0/24` | `2a02:8010:8006:3a51::/64` |
| kub3-dev-bridgend-01b-admin     | `52`  | `172.23.52.0/24` | `2a02:8010:8006:3a52::/64` |
| kub3-dev-bridgend-01b-worker    | `53`  | `172.23.53.0/24` | `2a02:8010:8006:3a53::/64` |
| kub3-dev-bridgend-01c-admin     | `54`  | `172.23.54.0/24` | `2a02:8010:8006:3a54::/64` |
| kub3-dev-bridgend-01c-worker    | `55`  | `172.23.55.0/24` | `2a02:8010:8006:3a55::/64` |
| kub3-dev-bridgend-01a-services  | `BGP` | `172.23.58.0/23` | `2a02:8010:8006:3a58::/64` |

## Firewall Configuration
