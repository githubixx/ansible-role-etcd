ansible-role-etcd
=================

This Ansible playbook is used in [Kubernetes the not so hard way with Ansible (at Scaleway) - Part 5 - etcd cluster](https://www.tauceti.blog/post/kubernetes-the-not-so-hard-way-with-ansible-at-scaleway-part-5/). Have a look there for more information.

Installes a etcd cluster. HINT: This playbook does NOT reload or restart the etcd cluster nodes after the systemd service file was changed! This is intentional! It would be a very bad idea to restart all etcd nodes at the same time. So if the `etcd.service` file has changed restart/reload etcd by hand one node after the other and check log output if the node joined the cluster again afterwards! As a side node: The script will issue a `systemctl daemon-reload` after the etcd service file was changed so that at least systemd is aware of the changed file and you don't take care about that. So a reboot of a etcd node would also active the new configuration.

Versions
--------

I tag every release and try to stay with [semantic versioning](http://semver.org) (well some kind of). If you want to use the role I recommend to checkout the latest tag. The master branch is basically development while the tags mark stable releases. But in general I try to keep master in good shape too. A tag `r1.0.0_v3.2.8` means this is release 1.0.0 of this role and it's meant to be used with etcd version 3.2.8. If the role itself changes `rX.Y.Z` will increase. If the etcd version changes `vX.Y.Z` will increase. This allows to tag bugfixes and new major versions of the role while it's still developed for a specific etcd release.

Changelog
---------
**r2.0.0_v3.2.13**

- updated etcd to 3.2.13
- added new etcd flags (see role variables below)
- change default for `k8s_ca_conf_directory` (see role variables below). If you already defined `k8s_ca_conf_directory` by yourself in `group_vars/k8s.yml` or `group_vars/all.yml` nothing changes for you.
- more documentation for role variables

**r1.0.0_v3.2.8**

- updated etcd to 3.2.8
- rename `local_cert_dir` to `k8s_ca_conf_directory` and change default location
- smaller changes needed for Kubernetes v1.8

Requirements
------------

This playbook requires that you already created some certificates for etcd (see [Kubernetes the not so hard way with Ansible (at Scaleway) - Part 4 - Certificate authority (CA)](https://www.tauceti.blog/post/kubernetes-the-not-so-hard-way-with-ansible-at-scaleway-part-4/)). The playbook searches the certificates in `k8s_ca_conf_directory` on the host this playbook runs.

Role Variables
--------------

```
# The directory from where to copy the K8s certificates. By default this
# will expand to user's LOCAL $HOME (the user that run's "ansible-playbook ..."
# plus "/k8s/certs". That means if the user's $HOME directory is e.g.
# "/home/da_user" then "k8s_ca_conf_directory" will have a value of
# "/home/da_user/k8s/certs".
k8s_ca_conf_directory: "{{ '~/k8s/certs' | expanduser }}"

# etcd version
etcd_version: "3.2.13"
# Port where etcd is listening for clients
etcd_client_port: "2379"
# Port where etcd is listening for it's peer's
etcd_peer_port: "2380"
# Interface to bind etcd ports to
etcd_interface: "tap0"
# Initial cluster token for the etcd cluster during bootstrap.
etcd_initial_cluster_token: "etcd-cluster-0"
# Initial cluster state ('new' or 'existing')
etcd_initial_cluster_state: "new"
# Directroy for etcd configuration
etcd_conf_dir: "/etc/etcd"
# Directory to store downloaded etcd archive
# Should not be deleted to avoid downloading over and over again
etcd_download_dir: "/opt/etcd"
# Directroy to store etcd binaries
etcd_bin_dir: "/usr/local/bin"
# etcd data directory (etcd database files so to say)
etcd_data_dir: "/var/lib/etcd"
# Dedicated wal directory ("" means no seperated WAL directory)
etcd_wal_dir: ""
# Auto compaction retention in hour. 0 means disable auto compaction.
etcd_auto_compaction_retention: "0"
# Number of committed transactions to trigger a snapshot to disk
etcd_snapshot_count: "100000"
# Time (in milliseconds) of a heartbeat interval
etcd_heartbeat_interval: "100"
# Time (in milliseconds) for an election to timeout. See tuning documentation for details
etcd_election_timeout: "1000"
# Maximum number of snapshot files to retain (0 is unlimited)
etcd_max_snapshots: "5"
# Maximum number of wal files to retain (0 is unlimited)
etcd_max_wals: "5"
# Comma-separated whitelist of origins for CORS (cross-origin resource sharing)
etcd_cors: ""
# Raise alarms when backend size exceeds the given quota (0 defaults to low space quota)
etcd_quota_backend_bytes: "0"
# Specify a particular log level for each etcd package (eg: 'etcdmain=CRITICAL,etcdserver=DEBUG')
etcd_log_package_levels: ""
# Specify 'stdout' or 'stderr' to skip journald logging even when running under systemd
etcd_log_output: "default"

# Certificate authority and certificate files for etcd 
etcd_certificates:
  - ca-etcd.pem        # client server TLS trusted CA key file/peer server TLS trusted CA file
  - ca-etcd-key.pem    # CA key file
  - cert-etcd.pem      # peer server TLS cert file
  - cert-etcd-key.pem  # peer server TLS key file
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
