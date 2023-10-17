ansible-role-etcd
=================

This Ansible role is used in [Kubernetes the not so hard way with Ansible - etcd cluster](https://www.tauceti.blog/post/kubernetes-the-not-so-hard-way-with-ansible-etcd/). But it can be used without a Kubernetes cluster of course.

Installs a etcd cluster. HINT: This playbook does NOT reload or restart the etcd cluster processes after the systemd service file was changed! This is intentional! It would be a very bad idea to restart all etcd processes at the same time. So if the `etcd.service` file has changed restart/reload etcd by manually one node after the other and check log output if the node joined the cluster again afterwards! Of course this process can be automated too but it's currently not part of this role.
As a side node: The script will issue a `systemctl daemon-reload` after the etcd service file was changed so that at least systemd is aware of the changed file and you don't take care about that. So a reboot of a etcd node would also active the new configuration.

Upgrading a etcd cluster which was installed by this role is described in [here](https://www.tauceti.blog/posts/kubernetes-the-not-so-hard-way-with-ansible-upgrading-kubernetes/#etcd).

Versions
--------

I tag every release and try to stay with [semantic versioning](http://semver.org). If you want to use the role I recommend to checkout the latest tag. The master branch is basically development while the tags mark stable releases. But in general I try to keep master in good shape too. A tag `13.0.0+3.5.9` means this is release `13.0.0` of this role and it's meant to be used with etcd version `3.5.9` (but should work with newer versions also). If the role itself changes `X.Y.Z` before `+` will increase. If the etcd version changes `X.Y.Z` after `+` will increase. This allows to tag bugfixes and new major versions of the role while it's still developed for a specific etcd release.

Changelog
---------

see [CHANGELOG.md](https://github.com/githubixx/ansible-role-etcd/blob/master/CHANGELOG.md)

Requirements
------------

This role requires that you already created some certificates for `etcd` (see [Kubernetes the not so hard way with Ansible - Certificate authority (CA)](https://www.tauceti.blog/post/kubernetes-the-not-so-hard-way-with-ansible-certificate-authority/) and Ansible role [kubernetes_ca](https://galaxy.ansible.com/ui/standalone/roles/githubixx/kubernetes_ca/)). The playbook searches the certificates in `etcd_ca_conf_directory` on the host this playbook runs. Of course you can create the certificates on your own (see [Generate self-signed certificates](https://github.com/coreos/docs/blob/master/os/generate-self-signed-certificates.md) - Git repository is archived but information is still valid).

Role Variables
--------------

```yaml
# The directory from where to copy the etcd certificates. By default this
# will expand to user's LOCAL $HOME (the user that run's "ansible-playbook ..."
# plus "/etcd-certificates". That means if the user's $HOME directory is e.g.
# "/home/da_user" then "etcd_ca_conf_directory" will have a value of
# "/home/da_user/etcd-certificates".
etcd_ca_conf_directory: "{{ '~/etcd-certificates' | expanduser }}"

# etcd Ansible group
etcd_ansible_group: "k8s_etcd"

# etcd version
etcd_version: "3.5.9"

# Port where etcd listening for clients
etcd_client_port: "2379"

# Port where etcd is listening for it's peer's
etcd_peer_port: "2380"

# Interface to bind etcd ports to
etcd_interface: "tap0"

# Run etcd daemon as this user.
#
# Note 1: If you want to use an "etcd_peer_port" < 1024 you most probably need
# to run "etcd" as user "root".
# Note 2: If the user specified in "etcd_user" does not exist then the role
# will create it. Only if the user already exists the role will not create it
# but it will adjust it's UID/GID and shell if specified (see settings below).
# Additionally if "etcd_user" is "root" then this role wont touch the user
# at all.
etcd_user: "etcd"

# UID of user specified in "etcd_user". If not specified the next available
# UID from "/etc/login.defs" will be taken (see "SYS_UID_MAX" setting).
# etcd_user_uid: "999"

# Shell for specified user in "etcd_user". For increased security keep
# the default.
etcd_user_shell: "/bin/false"

# Specifies if the user specified in "etcd_user" will be a system user (default)
# or not. If "true" the "etcd_user_home" setting will be ignored. In general
# it makes sense to keep the default as there should be no need to login as
# the user that runs "etcd".
etcd_user_system: true

# Home directory of user specified in "etcd_user". Will be ignored if
# "etcd_user_system" is set to "true". In this case no home directory will
# be created. Normally not needed.
# etcd_user_home: "/home/etcd"

# Run etcd daemon as this group
#
# Note: If the group specified in "etcd_group" does not exist then the role
# will create it. Only if the group already exists the role will not create it
# but will adjust GID if specified in "etcd_group_gid" (see setting below).
etcd_group: "etcd"

# GID of group specified in "etcd_group". If not specified the next available
# GID from "/etc/login.defs" will be take (see "SYS_GID_MAX" setting).
# etcd_group_gid: "999"

# Specifies if the group specified in "etcd_group" will be a system group (default)
# or not.
etcd_group_system: true

# Directory for etcd configuration
etcd_conf_dir: "/etc/etcd"

# Permissions for directory for etcd configuration
etcd_conf_dir_mode: "0750"

# Owner of directory specified in "etcd_conf_dir"
etcd_conf_dir_user: "root"

# Group owner of directory specified in "etcd_conf_dir"
etcd_conf_dir_group: "{{ etcd_group }}"

# Directory to store downloaded etcd archive
# Should not be deleted to avoid downloading over and over again
etcd_download_dir: "/opt/etcd"

# Permissions for directory to store downloaded etcd archive
etcd_download_dir_mode: "0755"

# Owner of directory specified in "etcd_download_dir"
etcd_download_dir_user: "{{ etcd_user }}"

# Group owner of directory specified in "etcd_download_dir"
etcd_download_dir_group: "{{ etcd_group }}"

# Directory to store etcd binaries
#
# IMPORTANT: If you use the default value for "etcd_bin_dir" which is
# "/usr/local/bin" then the settings specified in "etcd_bin_dir_mode",
# "etcd_bin_dir_user" and "etcd_bin_dir_group" are ignored. This is
# done to prevent that the permissions of "/usr/local/bin" are changed.
# This directory normally exists already on every Linux installation
# and should not be changed.
# So please be careful if you specify a directory like "/usr/bin" or
# "/bin" as "etcd_bin_dir" as this will change the permissions of
# these directories and this is something you normally do not want.
etcd_bin_dir: "/usr/local/bin"

# Permissions for directory to store etcd binaries
etcd_bin_dir_mode: "0755"

# Owner of directory specified in "etcd_bin_dir"
etcd_bin_dir_user: "{{ etcd_user }}"

# Group owner of directory specified in "etcd_bin_dir"
etcd_bin_dir_group: "{{ etcd_group }}"

# etcd data directory (etcd database files so to say)
etcd_data_dir: "/var/lib/etcd"

# Permissions for directory to store etcd data
etcd_data_dir_mode: "0700"

# Owner of directory specified in "etcd_data_dir"
etcd_data_dir_user: "{{ etcd_user }}"

# Group owner of directory specified in "etcd_data_dir"
etcd_data_dir_group: "{{ etcd_group }}"

# Architecture to download and install
etcd_architecture: "amd64"

# Only change this if the architecture you are using is unsupported
# For more information, see this:
# https://github.com/etcd-io/website/blob/main/content/en/docs/v3.5/op-guide/supported-platform.md
etcd_allow_unsupported_archs: false

# By default etcd tarball gets downloaded from official
# etcd repository. This can be changed to some custom
# URL if needed. For more information which protocols
# can be used see:
# https://docs.ansible.com/ansible/latest/collections/ansible/builtin/get_url_module.html
# It's only important to keep the filename naming schema:
# "etcd-v{{ etcd_version }}-linux-{{ etcd_architecture }}.tar.gz"
etcd_download_url: "https://github.com/etcd-io/etcd/releases/download/v{{ etcd_version }}/etcd-v{{ etcd_version }}-linux-{{ etcd_architecture }}.tar.gz"

# By default the SHA256SUMS file is used to verify the
# checksum of the tarball archive. This can also be
# changed to your needs.
etcd_download_url_checksum: "sha256:https://github.com/coreos/etcd/releases/download/v{{ etcd_version }}/SHA256SUMS"

# Options for [Service] section. For more information see:
# https://www.freedesktop.org/software/systemd/man/systemd.service.html#Options
# The options below "Type=notify" are mostly security/sandbox related settings
# and limit the exposure of the system towards the unit's processes.
# https://www.freedesktop.org/software/systemd/man/systemd.exec.html
etcd_service_options:
  - User={{ etcd_user }}
  - Group={{ etcd_group }}
  - Restart=on-failure
  - RestartSec=5
  - Type=notify
  - ProtectHome=true
  - PrivateTmp=true
  - ProtectSystem=full
  - ProtectKernelModules=true
  - ProtectKernelTunables=true
  - ProtectControlGroups=true
  - CapabilityBoundingSet=~CAP_SYS_PTRACE

etcd_settings:
  "name": "{{ ansible_hostname }}"
  "cert-file": "{{ etcd_conf_dir }}/cert-etcd-server.pem"
  "key-file": "{{ etcd_conf_dir }}/cert-etcd-server-key.pem"
  "trusted-ca-file": "{{ etcd_conf_dir }}/ca-etcd.pem"
  "peer-cert-file": "{{ etcd_conf_dir }}/cert-etcd-peer.pem"
  "peer-key-file": "{{ etcd_conf_dir }}/cert-etcd-peer-key.pem"
  "peer-trusted-ca-file": "{{ etcd_conf_dir }}/ca-etcd.pem"
  "advertise-client-urls": "{{ 'https://' + hostvars[inventory_hostname]['ansible_' + etcd_interface].ipv4.address + ':' + etcd_client_port }}"
  "initial-advertise-peer-urls": "{{ 'https://' + hostvars[inventory_hostname]['ansible_' + etcd_interface].ipv4.address + ':' + etcd_peer_port }}"
  "listen-peer-urls": "{{ 'https://' + hostvars[inventory_hostname]['ansible_' + etcd_interface].ipv4.address + ':' + etcd_peer_port }}"
  "listen-client-urls": "{{ 'https://' + hostvars[inventory_hostname]['ansible_' + etcd_interface].ipv4.address + ':' + etcd_client_port + ',https://127.0.0.1:' + etcd_client_port }}"
  "peer-client-cert-auth": "true"            # Enable peer client cert authentication
  "client-cert-auth": "true"                 # Enable client cert authentication
  "initial-cluster-token": "etcd-cluster-0"  # Initial cluster token for the etcd cluster during bootstrap.
  "initial-cluster-state": "new"             # Initial cluster state ('new' or 'existing')
  "data-dir": "{{ etcd_data_dir }}"          # etcd data directory (etcd database files so to say)
  "wal-dir": ""                              # Dedicated wal directory ("" means no separated WAL directory)
  "auto-compaction-retention": "0"           # Auto compaction retention in hour. 0 means disable auto compaction.
  "snapshot-count": "100000"                 # Number of committed transactions to trigger a snapshot to disk
  "heartbeat-interval": "100"                # Time (in milliseconds) of a heartbeat interval
  "election-timeout": "1000"                 # Time (in milliseconds) for an election to timeout. See tuning documentation for details
  "max-snapshots": "5"                       # Maximum number of snapshot files to retain (0 is unlimited)
  "max-wals": "5"                            # Maximum number of wal files to retain (0 is unlimited)
  "quota-backend-bytes": "0"                 # Raise alarms when backend size exceeds the given quota (0 defaults to low space quota)
  "logger": "zap"                            # Specify ‘zap’ for structured logging or ‘capnslog’.
  "log-outputs": "systemd/journal"           # Specify 'stdout' or 'stderr' to skip journald logging even when running under systemd
  "enable-v2": "true"                        # enable v2 API to stay compatible with previous etcd 3.3.x (needed for flannel e.g.)
  "discovery-srv": ""                        # Discovery domain to enable DNS SRV discovery, leave empty to disable. If set, will override initial-cluster.

# Certificate authority and certificate files for etcd
etcd_certificates:
  - ca-etcd.pem               # certificate authority file
  - ca-etcd-key.pem           # certificate authority key file
  - cert-etcd-peer.pem        # peer TLS cert file
  - cert-etcd-peer-key.pem    # peer TLS key file
  - cert-etcd-server.pem      # server TLS cert file
  - cert-etcd-server-key.pem  # server TLS key file
```

The `etcd` default settings defined in `etcd_settings` can be overridden by defining a variable called `etcd_settings_user`. You can also add additional settings by using this variable. E.g. to override the default value for `log-output` setting and add a new setting like `grpc-keepalive-min-time` add the following settings to `group_vars/k8s.yml`:

```yaml
etcd_settings_user:
  "log-output": "stdout"
  "grpc-keepalive-min-time": "10s"
```

Example Playbook
----------------

```yaml
- hosts: k8s_etcd
  roles:
    - githubixx.etcd
```

Testing
-------

This role has a small test setup that is created using [Molecule](https://github.com/ansible-community/molecule), libvirt (vagrant-libvirt) and QEMU/KVM. Please see my blog post [Testing Ansible roles with Molecule, libvirt (vagrant-libvirt) and QEMU/KVM](https://www.tauceti.blog/posts/testing-ansible-roles-with-molecule-libvirt-vagrant-qemu-kvm/) how to setup. The test configuration is [here](https://github.com/githubixx/ansible-role-etcd/tree/master/molecule/default).

Afterwards Molecule can be executed:

```bash
molecule converge
```

This will setup a three virtual machines (VM) with Ubuntu 20.04/22.04 and installs an `etcd` cluster. A small verification step is also included:

```bash
molecule verify
```

To clean up run

```bash
molecule destroy
```

License
-------

GNU GENERAL PUBLIC LICENSE Version 3

Author Information
------------------

[http://www.tauceti.blog](http://www.tauceti.blog)
