Changelog
---------

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

- move some variables into etcd_settings dictionary. As they're not needed outside of the etcd role there is no need to keep them seperate.

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
