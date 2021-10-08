Role Name grafanaBuild
======================

This role is made with purpose to pull the relevant CAs, build and push the Prometheus Server image to Artifactory. The role will prune the docker daemon once the build is finished if docker daemon is running. This role runs on remote host.

Requirements
------------

No prerequicites for this roles it should be running directly.

Role Variables
--------------

For this role there are variables defined in the current role `roles/grafanaBuild/vars/main.yml` where the ports binds for the container are located and also the port validation.

Dependencies
------------

No dependencies.

Example Playbook
----------------

```bash
ansible-playbook \
	--tags grafanaBuild \
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


Grafana DataSource
------------------

In order for the user to be able to auto deploy the contianer and the container will auto create datasource the user needs to pass the correct CAs and configurations on the config file. Sample of the [example-data-source-config-file](https://grafana.com/docs/grafana/latest/administration/provisioning/#example-data-source-config-file) from the official documentation. Unfortunately what is officially documented (add the CA between double quotes) does not work. The solution that we came up is add a pipe after the key that you want to add followed by the int of white spaces from the key. Sample below:

```bash
$ cat roles/grafanaBuild/templates/datasource.yml.j2
# config file version
apiVersion: 1

# list of datasources that should be deleted from the database
deleteDatasources:
  - name: Prometheus
    orgId: 1

# list of datasources to insert/update depending
# whats available in the database
# https://grafana.com/docs/grafana/latest/administration/provisioning/#data-sources
datasources:
  # <string, required> name of the datasource. Required
  - name: Prometheus
    # <string, required> datasource type. Required
    type: prometheus
    # <string, required> access mode. proxy or direct (Server or Browser in the UI). Required
    access: proxy
    # <int> org id. will default to orgId 1 if not specified
    orgId: 1
    # <string, required> access mode. proxy or direct (Server or Browser in the UI). Required
    url: https://{{ prometheus.proxy.url }}
    # <string> database user, if used
    user:
    # <string> database name, if used
    database:
    # <bool> enable/disable basic auth
    basicAuth: true
    # <string> basic auth username, if used
    basicAuthUser: {{ user }}
    # <bool> enable/disable with credentials headers
    withCredentials: true
    # <bool> mark as default datasource. Max one per org
    isDefault: true
    # <map> fields that will be converted to json and stored in json_data
    jsonData:
      graphiteVersion: "1.1"
      tlsAuth: true
      tlsAuthWithCACert: true
      oauthPassThru: true
    # <string> json object of data that will be encrypted.
    secureJsonData:
      # https://stackoverflow.com/questions/44365613/adding-and-extracting-a-certificate-string-in-yml-file
      tlsCACert: |2
{{ grafanaCa.stdout }}
      tlsClientCert: |2
{{ grafanaCrt.stdout }}
      tlsClientKey: |2
{{ grafanaKey.stdout }}
      # <string> database password, if used
      password:
      # <string> basic auth password
      basicAuthPassword: {{ user }}
    version: 1
    # <bool> allow users to edit datasources from the UI.
    editable: true
```

It is easy to notice the we use the format `key: |2`. This indicates that we want to have 2 white spaces from the key, default if the int is ommited is 1. In order to prepare the CA for the file we need to append to the CA 8 white space characters on each line of the CA. Sample of the result that what we want to achieve at the end:

```bash
# config file version
apiVersion: 1

# list of datasources that should be deleted from the database
deleteDatasources:
  - name: Prometheus
    orgId: 1

# list of datasources to insert/update depending
# whats available in the database
# https://grafana.com/docs/grafana/latest/administration/provisioning/#data-sources
datasources:
  # <string, required> name of the datasource. Required
  - name: Prometheus
    # <string, required> datasource type. Required
    type: prometheus
    # <string, required> access mode. proxy or direct (Server or Browser in the UI). Required
    access: proxy
    # <int> org id. will default to orgId 1 if not specified
    orgId: 1
    # <string, required> access mode. proxy or direct (Server or Browser in the UI). Required
    url: https://{{ prometheus.proxy.url }}:{{ prometheus.conf.port }}
    # <string> database user, if used
    user:
    # <string> database name, if used
    database:
    # <bool> enable/disable basic auth
    basicAuth: true
    # <string> basic auth username, if used
    basicAuthUser: {{ user }}
    # <bool> enable/disable with credentials headers
    withCredentials: true
    # <bool> mark as default datasource. Max one per org
    isDefault: true
    # <map> fields that will be converted to json and stored in json_data
    jsonData:
      graphiteVersion: "1.1"
      tlsAuth: false
      tlsAuthWithCACert: true
      oauthPassThru: true
    # <string> json object of data that will be encrypted.
    secureJsonData:
      tlsCACert: |2
        -----BEGIN CERTIFICATE-----
        line 1
        line 2
        etc
        etc
        etc
        -----END CERTIFICATE-----
      # <string> database password, if used
      password:
      # <string> basic auth password
      basicAuthPassword: {{ user }}
    version: 1
    # <bool> allow users to edit datasources from the UI.
    editable: true
```

License
-------

BSD

Author Information
------------------

Athanasios Garyfalos. Email: athanasios.garyfalos@cpan.org
