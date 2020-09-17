# IaC / Ansible / Roles / Ceph client for Kubernetes

Setup a Ceph `StorageClass` on Kubernetes clusters.

You can include two different components when using this role:

1 - To setup the `StorageClass`:

```yaml
include_role:
  name: ceph_client_k8s
```

2 - To collect Ceph cluster key and cluster identifier:

```yaml
include_role:
  name: ceph_client_k8s
  tasks_from: ceph_facts.yml
```

## Tags

| Tag      | Description                                                                                     |
|----------|-------------------------------------------------------------------------------------------------|
| `apply`  | Create the `StorageClass`, associated `Secret` and update the `ceph-csi-config-rbd` `ConfigMap` |
| `delete` | Remove the `StorageClass`, associated `Secret` and update the `ceph-csi-config-rbd` `ConfigMap` |

## Variables

Generic configuration:

| Variable                               | Type               | Required | Defaut                           | Description                                                  |
|----------------------------------------|--------------------|----------|----------------------------------|--------------------------------------------------------------|
| `ceph_client_k8s_provider`             | `string`           | No       | `ceph-csi-rbd`                   | RBD provisioner (`ceph-csi-rbd` or `rbd` for in-tree driver) |
| `ceph_client_k8s_sc_namespace`         | `string`           | No       | `default`                        | `StorageClass` namespace                                     |
| `ceph_client_k8s_sc_name`              | `string`           | No       | `{{ ceph_client_k8s_provider }}` | `StorageClass` name                                          |
| `ceph_client_k8s_sc_default`           | `bool`             | No       | `true`                           | Set the `StorageClass` as the default one                    |
| `ceph_client_k8s_mons`                 | `list` of `string` | Yes      | -                                | List of Ceph monitors                                        |
| `ceph_client_k8s_pool`                 | `string`           | No       | `cephfs_data`                    | Ceph pool name                                               |
| `ceph_client_k8s_fstype`               | `string`           | No       | `ext4`                           | File system type for new volumes                             |
| `ceph_client_k8s_reclaimPolicy`        | `string`           | No       | `Delete`                         | Volume's `ReclaimPolicy`                                     |
| `ceph_client_k8s_allowVolumeExpansion` | `bool`             | No       | `true`                           | Allow or not the expansion of created volumes                |

Authentication:

| Variable                           | Type     | Required | Defaut                                 | Description                   |
|------------------------------------|----------|----------|----------------------------------------|-------------------------------|
| `ceph_client_k8s_secret_namespace` | `string` | No       | `default`                              | `Secret` namespace            |
| `ceph_client_k8s_secret_name`      | `string` | No       | `{{ ceph_client_k8s_sc_name }}-secret` | `Secret` name                 |
| `ceph_client_k8s_user`             | `string` | Yes      | -                                      | Ceph pool user name           |
| `ceph_client_k8s_key`              | `string` | Yes      | -                                      | Ceph pool user key (password) |

CSI-specific:

| Variable                        | Type     | Required | Defaut        | Description                                |
|---------------------------------|----------|----------|---------------|--------------------------------------------|
| `ceph_client_k8s_csi_namespace` | `string` | No       | `kube-system` | Namespace where `ceph-csi-rbd` is deployed |
| `ceph_client_k8s_csi_cid`       | `string` | Yes      | -             | Ceph cluster identifier (name)             |

## Templates

| Template                           | Description                                                                          |
|------------------------------------|--------------------------------------------------------------------------------------|
| `ceph-csi-rbd/cluster.json.j2`     | A cluster configuration block; Will be injected into `ceph-csi-rbd/configmap.yml.j2` |
| `ceph-csi-rbd/configmap.yml.j2`    | Clusters configuration objects (holds all clusters definition)                       |
| `ceph-csi-rbd/secret.yml.j2`       | Secret template                                                                      |
| `ceph-csi-rbd/storageclass.yml.j2` | StorageClass template                                                                |

## Tasks sequence

```text
main.yml
|
| ------------------------ tags: apply, delete --
|
\__ provider-{{ ceph_client_k8s_provider }}.yml
```
