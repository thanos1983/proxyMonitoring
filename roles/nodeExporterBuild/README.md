Role Name nodeExporterBuild
===========================

This role is made with purpose to pull the relevant CAs, build and push the Node Exporter image to Artifactory. The role will prune the docker daemon once the build is finished if docker daemon is running. This role run on local not to a remote host.

Requirements
------------

No prerequicites for this roles it should be running directly.

Role Variables
--------------

For this role there are variables defined in the current role `roles/nodeExporterBuild/vars/main.yml` where the ports binds for the container are located and also the port validation.

Dependencies
------------

No dependencies.

Example Playbook
----------------

```bash
ansible-playbook \
	--tags nodeExporterBuild \
	--skip-tags never \
	-i inventories/development/hosts.yml \
	-e <env_variable> \
	-e ansible_user=<username> \
	-e ansible_ssh_pass=<password> \
	-e target_hosts=<target_hosts> \
	roles/nodeExporterBuild/tasks/main.yml -v
```

Metrics
-------

If the user desires to test the metrics end point, due to all the security that is applied he / she needs to provide the CA plus the credentials. Sample:

`curl --cacert <ca.pem or ca.crt> -u username:password https://<node-name>:<port>/metrics | grep uname`

Sample:

```bash
$ curl --cacert CA.pem -u user:password https://host:port/metrics | grep uname
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0node_scrape_collector_duration_seconds{collector="uname"} 2.075e-05
node_scrape_collector_success{collector="uname"} 1
# HELP node_uname_info Labeled system information as provided by the uname system call.
# TYPE node_uname_info gauge
node_uname_info{domainname="(none)",machine="x86_64",nodename="host.my.domain",release="3.10.0-1160.31.1.el7.x86_64",sysname="Linux",version="#1 SMP Wed May 26 20:18:08 UTC 2021"} 1
100  154k    0  154k    0     0   474k      0 --:--:-- --:--:-- --:--:--  475k
```

License
-------

BSD

Author Information
------------------

Athanasios Garyfalos. Email: athanasios.garyfalos@cpan.org
