Role Name
=========

Installes etcd cluster. HINT: This playbook does NOT reload or restart the etcd cluster nodes after the systemd service file was changed! This is intentional! It would be a very bad idea to restart all etcd nodes at the same time. So after changes restart/reload etcd by hand one node after the other and check log output if the node joined the cluster again afterwards! As a side node: The script will issue a `systemctl daemon-reload` after the etcd service file was changed so that at least systemd is aware of the changed file and you don't take care about that.

Requirements
------------

This playbook requires that you already created some certificates for etcd (see [ansible-role-cfssl](https://github.com/githubixx/ansible-role-cfssl)). The playbook searches the certificates in `local_cert_dir` on the host this playbook runs.

Role Variables
--------------

```
local_cert_dir: /etc/cfssl

etcd_version: 3.0.15
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
  - ca.pem
  - kubernetes-key.pem
  - kubernetes.pem
```

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

```
- hosts: etcd

  roles:
    - githubixx.etcd
```

License
-------

GNU GENERAL PUBLIC LICENSE Version 3

Author Information
------------------

[http://www.tauceti.blog](http://www.tauceti.blog)
