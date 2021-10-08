Role Name docker
================

This role is responsibe for building the Dockerfile if the docker daemon is running.

Requirements
------------

No prerequicites for this roles it should be running directly.

Role Variables
--------------

For this role there are variables defined in the current role "roles/docker/vars/main.yml" and also on the group vars of the relevant environment e.g. tmp_nginx: "/tmp/NGINX".

Dependencies
------------

There are no other roles related to this role but the main.yml task is loading 3 more tasks to be completed sequentially. All tasks on this role will be executed only once since this role is simply for building the image on the remote host. The role is responsible for creading the directory for the configuration files, copy the conf files (relevant to the env e.g. development / production). Next step is to build the Docker file based on the keys that were gathered from the previous step vault.yml. Last task is to clear the directories after building the Dockerfile with all the required configuration files / keys etc.

Example Playbook
----------------

`ansible-playbook -i inventories/development/hosts.yml -e ansible_user=<username> -e target_hosts=<target_hosts> roles/docker/tasks/main.yml -v

License
-------

BSD

Author Information
------------------

Athanasios Garyfalos. Email: athanasios.garyfalos@cpan.org
