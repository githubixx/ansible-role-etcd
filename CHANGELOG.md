Changelog
---------

**13.1.0+3.5.9**

- `systemd` service `etcd` should start after network is online

**13.0.0+3.5.9**

**NOTE**: This version contains some potential breaking changes to increase security related settings. So please test thoroughly before upgrading!

- **BREAKING**: Remove support for Ubuntu `18.04` (reached EOL)
- **BREAKING**: This update introduces quite a few new variables mainly for managing permissions and for security. These variables are: `etcd_service_options`, `etcd_data_dir_group`, `etcd_data_dir_user`, `etcd_data_dir_mode`, `etcd_bin_dir_group`, `etcd_bin_dir_user`, `etcd_download_dir_group`, `etcd_download_dir_user`, `etcd_conf_dir_group`, `etcd_conf_dir_user`, `etcd_group_system`, `etcd_group_gid`, `etcd_group`, `etcd_user_home`, `etcd_user_system`, `etcd_user_shell`, `etcd_user_uid`, `etcd_user`. Please see `defaults/main.yml` what these variables are good for. In general the defaults should be fine. Previously `etcd` was running with `root` account which is not optimal. This was changed so that it runs with user `etcd` by default now as specified in `etcd_user`. The role will take care that all the directories and files will change their owner and group accordingly. In general upgrading should work pretty flawlessly but as said above please test!
- **BREAKING**: `etcd_service_options` contains options for the `[Service]` section in the systemd `etcd.service` file. Besides a few other settings in also includes security/sandbox related settings and limit the exposure of the system towards the unit's processes.
- update `etcd` to `v3.5.9`
- add support for Ubuntu `22.04`
- Molecule: rename scenario from `kvm` to `default`
- Molecule: rename `githubixx.kubernetes-ca` to `githubixx.kubernetes_ca`
- Molecule: remove provider options / add memory + cpu parameter to VMs
- Molecule: adjust settings + adding host variables to cover different settings
- fix ansible-lint issues

**12.2.0+3.5.8**

- update `etcd` to `v3.5.8`

**12.1.0+3.5.6**

- update `etcd` to `v3.5.6`
- introduce `etcd_download_url` variable to allow custom `etcd` download URL (contribution by @DanOPT)
- introduce `etcd_download_url_checksum` variable to allow custom checksum for download URL (contribution by @DanOPT)
- better variables formatting (only cosmetic change)
- add `.yamllint`

**12.0.0+3.5.4**

- introduce new variables: `etcd_conf_dir_mode`, `etcd_download_dir_mode` and `etcd_bin_dir_mode` (see README)
- fix various `ansible-lint` issues
- add Github release action to push new release to Ansible Galaxy

**11.3.0+3.5.4**

- This is mainly a "cosmetic" change. Makes Ansible's linter `ansible-lint` happy, fixes a few typos and use FQDN module names

**11.2.0+3.5.4**

- Add `discovery-srv` setting for initial cluster setup (contribution by @cgoubert )

**11.1.0+3.5.4**

- update `etcd` to `v3.5.4`
- introduce `etcd_ansible_group` variable to allow setting the Ansible group variable for the etcd hosts. The group was formerly hardcoded to `k8s_etcd`.
- add `no_log` to certificate copy task to avoid private key leakage
- add Molecule test
- checksum etcd binaries after download

**11.0.0+3.5.1**

- update `etcd` to `v3.5.1`
- remove `log-package-levels` setting from `etcd_settings` as etcd `3.5` does not like empty values for this parameter. So if you need this parameter just add it to `etcd_settings_user` with a sensible value. Otherwise `etcd` wont start.
- remove unneeded files/directories
- remove Ubuntu 16.04 support
- fix typos

**10.2.0+3.4.14**

- add support for multiple architectures

**10.1.0+3.4.14**

- update `etcd` to `v3.4.14`
- `etcd_data_dir` permissions changed to `0700`. Before the permissions were not set so in most cases that ended up with `0755`. This was needed because of https://github.com/etcd-io/etcd/blob/master/CHANGELOG-3.4.md#breaking-changes

**10.0.0+3.4.7**

- changed some default values for `etcd_settings`. `(cert|key)-file` and `peer-(cert|key)-file` now uses different certificates:

```yaml
"cert-file": "{{etcd_conf_dir}}/cert-etcd-server.pem"
"key-file": "{{etcd_conf_dir}}/cert-etcd-server-key.pem"
"peer-cert-file": "{{etcd_conf_dir}}/cert-etcd-peer.pem"
"peer-key-file": "{{etcd_conf_dir}}/cert-etcd-peer-key.pem"
```

Therefore `etcd_certificates` list was also adjusted accordingly.

**9.1.0+3.4.7**

- enable v2 API again like in `etcd` v3.3.x. `etcd` v3.4.x disables v2 API by default (see https://github.com/etcd-io/etcd/blob/master/Documentation/upgrades/upgrade_3_4.md#make-etcd---enable-v2false-default). v2 API is needed for `flannel` e.g.

**Removal of old tags**

Removed old tags below as the format is not supported by Ansible Galaxy and also not compatible with semver.org:

```plain
r1.0.0_v3.2.8
r2.0.0_v3.2.13
r3.0.0_v3.2.13
r3.1.0_v3.2.13
r4.0.0_v3.2.13
r4.1.0_v3.2.13
r4.2.0_v3.2.13
r4.2.1_v3.2.13
r5.0.0_v3.2.13
r6.0.0_v3.2.18
r6.0.1_v3.2.24
```

**9.0.0+3.4.7**

- upgrade to `etcd` v3.4.7 (latest version supported/recommended for Kubernetes v1.17)
- rename deprecated log-output flag to log-outputs
- remove flag `--cors=""` as this causes `etcd` to fail to start
- set `--log-outputs="systemd/journal"` and add flag `--logger="zap"` (for structured logging) as mentioned in [CHANGELOG-3.4](https://github.com/etcd-io/etcd/blob/master/CHANGELOG-3.4.md#etcd-server-4)

**8.0.0+3.3.13**

- upgrade to `etcd` v3.3.13 (latest version supported/recommended for Kubernetes v1.14)

**7.0.0+3.2.24**

- use correct semantic versioning as described in https://semver.org. Needed for Ansible Galaxy importer as it now insists on using semantic versioning.
- make Ansible linter happy
- no major changes but decided to start a new major release as versioning scheme changed quite heavily

**r6.0.1_v3.2.24**

- upgrade to `etcd` v3.2.24 (latest version supported/recommended for Kubernetes v1.12)

**r6.0.0_v3.2.18**

- upgrade to `etcd` v3.2.18 (latest version supported/recommended for Kubernetes v1.11)

**r5.0.0_v3.2.13**

- rename variable `k8s_ca_conf_directory` to `etcd_ca_conf_directory`. As this role can be used standalone with Kubernetes (as I do in the blog post mentioned above) the former name makes no sense. For people who used this role before just set `etcd_ca_conf_directory: "{{k8s_ca_conf_directory}}"` in `group_vars/all.yml` and you have the same behavior as before (granted that you have `k8s_ca_conf_directory` also set if you don't use the default values ;-) .

**r4.2.1_v3.2.13**

- works with Ubuntu 18.04
- update README

**r4.2.0_v3.2.13**

- changed `listen-client-urls` scheme for 127.0.0.1 from http to https

**r4.1.0_v3.2.13**

- use full path for destination in download etcd task
- chown/chgrp to "root" user for unarchived etcd files

**r4.0.0_v3.2.13**

- fix bug `etcd_data_dir` variable missing

**r3.1.0_v3.2.13**

- move some variables into etcd_settings dictionary. As they're not needed outside of the etcd role there is no need to keep them separate.

**r3.0.0_v3.2.13**

- introduce flexible etcd parameter settings via `etcd_settings/etcd_settings_user` variables. This way all flags/settings of the current and future etcd version's can be set and there is no need to adjust the etcd systemd service file template with every release

**r2.0.0_v3.2.13**

- updated etcd to 3.2.13
- added new etcd flags (see role variables below)
- change default for `k8s_ca_conf_directory` (see role variables below). If you already defined `k8s_ca_conf_directory` by yourself in `group_vars/k8s.yml` or `group_vars/all.yml` nothing changes for you
- more documentation for role variables

**r1.0.0_v3.2.8**

- updated etcd to 3.2.8
- rename `local_cert_dir` to `k8s_ca_conf_directory` and change default location
- smaller changes needed for Kubernetes v1.8
