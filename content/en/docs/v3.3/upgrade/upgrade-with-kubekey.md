---
title: "Upgrade with KubeKey"
keywords: "Kubernetes, upgrade, KubeSphere, 3.3, KubeKey"
description: "Use KubeKey to upgrade Kubernetes and KubeSphere."
linkTitle: "Upgrade with KubeKey"
weight: 7200
---
KubeKey is recommended for users whose KubeSphere and Kubernetes were both installed by [KubeKey](../../installing-on-linux/introduction/kubekey/). If your Kubernetes cluster was provisioned by yourself or cloud providers, refer to [Upgrade with ks-installer](../upgrade-with-ks-installer/).

This tutorial demonstrates how to upgrade your cluster using KubeKey.

## Prerequisites

- You need to have a KubeSphere cluster running v3.2.x. If your KubeSphere version is v3.1.x or earlier, upgrade to v3.2.x first.
- Read [Release Notes for 3.3.1](../../../v3.3/release/release-v331/) carefully.
- Back up any important component beforehand.
- Make your upgrade plan. Two scenarios are provided in this document for [all-in-one clusters](#all-in-one-cluster) and [multi-node clusters](#multi-node-cluster) respectively.

## Major Updates

In KubeSphere 3.3.1, some changes have made on built-in roles and permissions of custom roles. Therefore, before you upgrade KubeSphere to 3.3.1, please note the following:

   - Change of built-in roles: Platform-level built-in roles `users-manager` and `workspace-manager` are removed. If an existing user has been bound to `users-manager` or `workspace-manager`, its role will be changed to `platform-regular` after the upgrade is completed. Role `platform-self-provisioner` is added. For more information about built-in roles, refer to [Create a user](../../quick-start/create-workspace-and-project).

   - Some permission of custom roles are removed:
       - Removed permissions of platform-level custom roles: user management, role management, and workspace management.
       - Removed permissions of workspace-level custom roles: user management, role management, and user group management.
       - Removed permissions of namespace-level custom roles: user management and role management.
       - After you upgrade KubeSphere to 3.3.1, custom roles will be retained, but removed permissions of the custom roles will be revoked.

## Download KubeKey

Follow the steps below to download KubeKey before you upgrade your cluster.

{{< tabs >}}

{{< tab "Good network connections to GitHub/Googleapis" >}}

Download KubeKey from its [GitHub Release Page](https://github.com/kubesphere/kubekey/releases) or use the following command directly.

```bash
curl -sfL https://get-kk.kubesphere.io | VERSION=v3.0.2 sh -
```

{{</ tab >}}

{{< tab "Poor network connections to GitHub/Googleapis" >}}

Run the following command first to make sure you download KubeKey from the correct zone.

```bash
export KKZONE=cn
```

Run the following command to download KubeKey:

```bash
curl -sfL https://get-kk.kubesphere.io | VERSION=v3.0.2 sh -
```

{{< notice note >}}

After you download KubeKey, if you transfer it to a new machine also with poor network connections to Googleapis, you must run `export KKZONE=cn` again before you proceed with the steps below.

{{</ notice >}} 

{{</ tab >}}

{{</ tabs >}}

{{< notice note >}}

The commands above download the latest release (v3.0.2) of KubeKey. You can change the version number in the command to download a specific version.

{{</ notice >}} 

Make `kk` executable:

```bash
chmod +x kk
```

## Upgrade KubeSphere and Kubernetes

Upgrading steps are different for single-node clusters (all-in-one) and multi-node clusters.

{{< notice info >}}

When upgrading Kubernetes, KubeKey will upgrade from one MINOR version to the next MINOR version until the target version. For example, you may see the upgrading process going from 1.16 to 1.17 and to 1.18, instead of directly jumping to 1.18 from 1.16.

{{</ notice >}}

### All-in-one cluster

Run the following command to use KubeKey to upgrade your single-node cluster to KubeSphere 3.3 and Kubernetes v1.22.12:

```bash
./kk upgrade --with-kubernetes v1.22.12 --with-kubesphere v3.3.1
```

To upgrade Kubernetes to a specific version, explicitly provide the version after the flag `--with-kubernetes`. Available versions are v1.19.x, v1.20.x, v1.21.x, * v1.22.x,  * v1.23.x， and v1.24.x. For Kubernetes versions with an asterisk, some features of edge nodes may be unavailable due to incompatability. Therefore, if you want to use edge nodes, you are advised to install Kubernetes v1.21.x or earlier.
### Multi-node cluster

#### Step 1: Generate a configuration file using KubeKey

This command creates a configuration file `sample.yaml` of your cluster.

```bash
./kk create config --from-cluster
```

{{< notice note >}}

It assumes your kubeconfig is allocated in `~/.kube/config`. You can change it with the flag `--kubeconfig`.

{{</ notice >}}

#### Step 2: Edit the configuration file template

Edit `sample.yaml` based on your cluster configuration. Make sure you replace the following fields correctly.

- `hosts`: The basic information of your hosts (hostname and IP address) and how to connect to them using SSH.
- `roleGroups.etcd`: Your etcd nodes.
- `controlPlaneEndpoint`: Your load balancer address (optional).
- `registry`: Your image registry information (optional).

{{< notice note >}}

For more information, see [Edit the configuration file](../../installing-on-linux/introduction/multioverview/#2-edit-the-configuration-file) or refer to the `Cluster` section of [the complete configuration file](https://github.com/kubesphere/kubekey/blob/release-2.2/docs/config-example.md) for more information.

{{</ notice >}}

#### Step 3: Upgrade your cluster
The following command upgrades your cluster to KubeSphere 3.3 and Kubernetes v1.22.12:

```bash
./kk upgrade --with-kubernetes v1.22.12 --with-kubesphere v3.3.1 -f sample.yaml
```

To upgrade Kubernetes to a specific version, explicitly provide the version after the flag `--with-kubernetes`. Available versions are v1.19.x, v1.20.x, v1.21.x, * v1.22.x,  * v1.23.x， and v1.24.x. For Kubernetes versions with an asterisk, some features of edge nodes may be unavailable due to incompatability. Therefore, if you want to use edge nodes, you are advised to install Kubernetes v1.21.x or earlier.

{{< notice note >}}

To use new features of KubeSphere 3.3, you may need to enable some pluggable components after the upgrade.

{{</ notice >}} 