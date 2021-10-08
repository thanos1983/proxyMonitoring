Role Name nginxBuild
====================

This role is made with purpose to pull the relevant CAs, build and push the NGINX image to Artifactory. The role will prune the docker daemon once the build is finished if docker daemon is running. This role run on local not to a remote host.

Requirements
------------

No prerequicites for this roles it should be running directly.

Role Variables
--------------

For this role there are variables defined in the current role `roles/nginxBuild/vars/main.yml` where the ports binds for the container are located and also the port validation.

Dependencies
------------

No dependencies.

Example Playbook
----------------

```bash
ansible-playbook \
	--tags nginxBuild \
	--skip-tags never \
	-i inventories/development/hosts.yml \
	-e <env_variable> \
	-e ansible_user=<username> \
	-e ansible_ssh_pass=<password> \
	-e target_hosts=<target_hosts> \
	roles/nginxBuild/tasks/main.yml -v
```

License
-------

BSD

Author Information
------------------

Athanasios Garyfalos. Email: athanasios.garyfalos@cpan.org
