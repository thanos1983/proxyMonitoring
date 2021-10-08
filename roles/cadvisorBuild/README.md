Role Name cadvisorBuild
=======================

This role is made with purpose to pull the relevant CAs, build and push the Node Exporter image to Artifactory. The role will prune the docker daemon once the build is finished if docker daemon is running. This role run on local not to a remote host.

Requirements
------------

No prerequicites for this roles it should be running directly.

Role Variables
--------------

For this role there are variables defined in the current role `roles/cadvisorBuild/vars/main.yml` where the ports binds for the container are located and also the port validation.

Dependencies
------------

No dependencies.

Example Playbook
----------------

```bash
ansible-playbook \
	--tags cadvisorBuild \
	--skip-tags never \
	-i inventories/development/hosts.yml \
	-e <env_variable> \
	-e ansible_user=<username> \
	-e ansible_ssh_pass=<password> \
	-e target_hosts=<target_hosts> \
	roles/cadvisorBuild/tasks/main.yml -v
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

htdigest
--------

Sample of how to produce a file with [htdigest](https://httpd.apache.org/docs/2.4/programs/htdigest.html). In Centos / RH we need to install httpd-tools package. Sample

```bash
sudo yum install httpd-tools -y
htdigest -c test.htdigest.j2 localhost support-dev
```

CAs from Consul Vault
---------------------

On this role we will be using the same CAs as NodeExporter as both roles serve exactly the same purpose. The difference with each other is that cadvisor has the ability also to scrape metrics for containers apart from only the node.

License
-------

BSD

Author Information
------------------

Athanasios Garyfalos. Email: athanasios.garyfalos@cpan.org
