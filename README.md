ansible-role-etcd
=================

This Ansible playbook is used in [Kubernetes the not so hard way with Ansible (at Scaleway) - Part 5 - etcd cluster](https://www.tauceti.blog/post/kubernetes-the-not-so-hard-way-with-ansible-at-scaleway-part-5/). Have a look there for more information.

Installes a etcd cluster. HINT: This playbook does NOT reload or restart the etcd cluster nodes after the systemd service file was changed! This is intentional! It would be a very bad idea to restart all etcd nodes at the same time. So after changes restart/reload etcd by hand one node after the other and check log output if the node joined the cluster again afterwards! As a side node: The script will issue a `systemctl daemon-reload` after the etcd service file was changed so that at least systemd is aware of the changed file and you don't take care about that.

Versions
--------

I tag every release and try to stay with [semantic versioning](http://semver.org). If you want to use the role I recommend to checkout the latest tag. The master branch is basically development while the tags mark stable releases. But in general I try to keep master in good shape too. A tag `r1.0.0_v3.2.8` means this is release 1.0.0 of this role and it's meant to be used with etcd version 3.2.8. If the role itself changes `rX.Y.Z` will increase. If the etcd version changes `vX.Y.Z` will increase. This allows to tag bugfixes and new major versions of the role while it's still developed for a specific etcd release.

Requirements
------------

This playbook requires that you already created some certificates for etcd (see [Kubernetes the not so hard way with Ansible (at Scaleway) - Part 4 - Certificate authority (CA)](https://www.tauceti.blog/post/kubernetes-the-not-so-hard-way-with-ansible-at-scaleway-part-4/)). The playbook searches the certificates in `k8s_ca_conf_directory` on the host this playbook runs.

Role Variables
--------------

```
k8s_ca_conf_directory: /etc/k8s/certs

etcd_version: 3.2.8
etcd_client_port: 2379
etcd_peer_port: 2380
etcd_interface: tap0
etcd_initial_cluster_token: etcd-cluster-0
etcd_initial_cluster_state: new
etcd_name: etcd_kubernetes
etcd_conf_dir: /etc/etcd
etcd_download_dir: /opt/etcd
etcd_bin_dir: /usr/local/bin
etcd_data_dir: /var/lib/etcd
etcd_certificates:
  - ca-etcd.pem
  - ca-etcd-key.pem
  - cert-etcd.pem
  - cert-etcd-key.pem
```

Example Playbook
----------------

```
- hosts: k8s_etcd
  roles:
    - githubixx.etcd
```

License
-------

GNU GENERAL PUBLIC LICENSE Version 3

Author Information
------------------

[http://www.tauceti.blog](http://www.tauceti.blog)
