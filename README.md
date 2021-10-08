# Nginx / Prometheus / Grafan full setup

Requirements
------------

This script is written in Ansible 2.10.4 and with Python 3.8.0. Minimal requirements are Ansible 2.10.0 and Python 3.6.8. 

#### Restart command

docker restart \<container name\>

#### Update secrets
Configuration are applied to the Dockerfile including CAs fetched from [Vault](https://www.vaultproject.io/docs/configuration/storage/consul).The procedure is splitted into two pieces (build and deploy). The build part creates the configuration files and copies them to the Dockerfile. The CAs for any role are also copied during the build and not mounted during deployment.

### Docker mounts
nginx currently mounts 1 dir for logs:

1. /data/sample/nginx/logs

Also the other container mount some dirs read relevant README file on each role.

# bash build.sh script relevantly bash deploy.sh script

#### Roles

The script is splitted in two steps build and deploy.

Ansible does everything automated for us. First step build.sh script. Ansible retrieves from [Vault](https://www.vaultproject.io/docs/configuration/storage/consul) the CA certifications for development and production environment.

Next step based on retrieved and decoded files the script collects the necessary configuration files and builds the Docker file.

The build.yml is the source of the script where all build roles are imported. The choice of role is happening through the ansible tags:

<ul> 
 <li>cadvisorBuild</li>
 <li>grafanaBuild</li>
 <li>nginxBuild</li>
 <li>nodeExporterBuild</li>
 <li>prometheusBuild</li>
</ul>

The second script deploy.sh will first create all necessary directories (e.g. /data/sample/nginx/logs) if the tag is becomeuser and after will pull the image that we builded based on release from Bamboo and deploy on all nodes (development / production).

The deploy.yml is the endpoint of the script where the roles to be applied are also based on ansible tags:

<ul>
 <li>cadvisorDeploy</li>
 <li>grafanaDeploy</li>
 <li>nginxDeploy</li>
 <li>nodeExporterDeploy</li>
 <li>prometheusDeploy</li>
</ul>

The naming and release numbers are coming from Bamboo. __Note__ every build and Deployment will have a different version so we can keep track of differences.

#### Running Container as downgraded user (non root)

Sample of container for sample user:
```bash
[root@65cbd18ede2d /]# ps axo pid,pcpu,pmem,vsz,rss,tty,stat,start,time,comm,user
  PID %CPU %MEM    VSZ   RSS TT       STAT  STARTED     TIME COMMAND         USER
    1  0.0  0.0 124348   308 ?        Ss     Feb 25 00:00:00 nginx           sample
   21  0.2  0.2 126736  4392 ?        S      Feb 25 00:11:24 nginx           sample
   22  0.2  0.2 130720  4672 ?        S      Feb 25 00:10:45 nginx           sample
   29  0.0  0.1  11828  1896 pts/0    Ss   12:04:20 00:00:00 bash            root
   49  0.0  0.0  51752  1704 pts/0    R+   12:08:09 00:00:00 ps              root
```
