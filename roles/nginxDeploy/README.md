Role Name nginxDeploy
=====================

This role is responsible to deploy the NGINX image that is build and pushed to Artifactory. The role will stop and prunte the existing container(s) if docker daemon is running.

Requirements
------------

No prerequicites for this roles it should be running directly.

Role Variables
--------------

For this role there are variables defined in the current role "roles/nginxDeploy/vars/main.yml" where the ports binds for the container are located and also the port validation.

Dependencies
------------

No dependencies either for this role.

Example Playbook
----------------

```bash
ansible-playbook \
	--tags nginxDeploy \
	--skip-tags never \
	-i inventories/development/hosts.yml \
	-e <env_variable> \
	-e ansible_user=<username> \
	-e ansible_ssh_pass=<password> \
	-e target_hosts=<target_hosts> \
	roles/nginxDeploy/tasks/main.yml -v
```

License
-------

BSD

Author Information
------------------

Athanasios Garyfalos. Email: athanasios.garyfalos@cpan.org
