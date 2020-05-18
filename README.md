# IaC / Ansible / Roles / Ceph client for Kubernetes

Setup a Ceph `StorageClass` on Kubernetes clusters.

## Usage

To automatically collect required Ceph cluster facts:

```yaml
include_role:
  name: ceph_client_k8s
  tasks_from: ceph_facts.yml
```

To setup the `StorageClass`:

```yaml
include_role:
  name: ceph_client_k8s
  tasks_from: main.yml
```

## Tags

| Tag      | Description                         |
|----------|-------------------------------------|
| `apply`  | Create the requested `StorageClass` |
| `delete` | Remove the requested `StorageClass` |

## Variables

Role variables are prefixed with `ceph_client_k8s_`.

| Variable                     | Type               | Required | Defaut                         | Description                                             |
|------------------------------|--------------------|----------|--------------------------------|---------------------------------------------------------|
| `ceph_client_k8s_mons_group` | `string`           | No       | `ceph_mons`                    | Ceph monitors inventory group                           |
| `ceph_client_k8s_mons`       | `list` of `string` | No       | `[]` or `ceph_client_k8s_mons` | List of Ceph monitors                                   |
| `ceph_client_k8s_user`       | `string`           | No       | `admin`                        | Ceph user name                                          |
| `ceph_client_k8s_key`        | `string`           | No       | `string`                       | Ceph user key                                           |
| `ceph_client_k8s_pool`       | `string`           | No       | `cephfs_data`                  | Ceph RBD pool                                           |
| `ceph_client_k8s_cid`        | `string`           | No       | `null`                         | Ceph cluster ID (only for Ceph's CSI RBD provisioner)   |
| `ceph_client_k8s_provider`   | `string`           | No       | `rbd`                          | K8S RBD provisioner (`rbd` or `ceph-csi-rbd`)           |
| `ceph_client_k8s_prefix`     | `string`           | No       | `rbd`                          | K8S resources name prefix (e.g. `rbd-sc`, `rbd-secret`) |
| `ceph_client_k8s_secret`     | `string`           | No       | `rbd-secret`                   | K8S `Secret` name                                       |
| `ceph_client_k8s_sc`         | `string`           | No       | `rbd-sc`                       | K8S `StorageClass` name                                 |

## Templates

| Template                       | Description                                                        |
|--------------------------------|--------------------------------------------------------------------|
| `provider-rbd.yml.j2`          | K8S manifest, using the standard provisioner (`kubernetes.io/rbd`) |
| `provider-ceph-csi-rbd.yml.j2` | K8S manifest, using Ceph's own provisioner (`rbd.csi.ceph.com`)    |

## Tasks sequence

```text
main.yml
|
| ------------------------ tags: apply, delete --
\__ provider-{{ ceph_client_k8s_provider }}.yml
```
