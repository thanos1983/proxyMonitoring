Role Name prometheusBuild
=========================

This role is made with purpose to pull the relevant CAs, build and push the Prometheus Server image to Artifactory. The role will prune the docker daemon once the build is finished if docker daemon is running. This role runs on remote host.

Requirements
------------

No prerequicites for this roles it should be running directly.

Role Variables
--------------

For this role there are variables defined in the current role `roles/prometheusBuild/vars/main.yml` where the ports binds for the container are located and also the port validation.

Dependencies
------------

No dependencies.

Example Playbook
----------------

```bash
ansible-playbook \
	--tags prometheusBuild \
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

`curl --cacert <ca.pem or ca.crt> -u username:password https://<node-name>:<port>/metrics`

License
-------

BSD

Author Information
------------------

Athanasios Garyfalos. Email: athanasios.garyfalos@cpan.org
