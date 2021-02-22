# registry

Project contains roles designed to set up Docker pull through caching registry container. 
Project is fragmented into three roles which can be run as a standalone depending on your current needs. 

### Brief explanation of roles listed in setup.yml

- certbot installs Certbot and SSL certificate for your domain.
- docker_install installs Docker from the official repository on Centos systems (It can also be applied on later Fedora systems and both Centos 7 and 8).
- docker_registry sets up Docker pull through registry container.

### Variables defaults

All variables are set up in `group_vars/all` as most are used by multiple roles. 
If you're using role as a standalone, you can set up variables in $role/defaults/main.yml or $role/vars/main.yml as describes in role's specific README.md files.

```

mount_dir: "/etc/docker/{{ registry_host }}" #path to directory where SSL certificates and Docker config file for your registry will be stored
registry_host: registry.domain.tld #hostname for your registry
ssl_email: user@domain.tld #administrative email address for your SSL certificates
registry_container_name: registry.domain.tld #name of your registry container; best practice would be to use the same one as registry hostname
registry_image: registry:2.1 #image for your registry container
host_listening_port: 5443 #Docker host port you are going to map container's port 433 to

```

### Local machine instructions

In order to use freshly built container as your pull through proxy for your local machine, please add following lines to `/etc/docker/daemon.json` file on your local machine with registry.domain.tld:5443 being registry hostname and Docker host port:

```

{
  "registry-mirrors": ["https://registry.domain.tld:5443"],
  "debug": true
}

```

Once this is done, restart Docker service on your local machine to apply changes:

```

service docker restart

or 

systemctl restart docker

```

To check if Docker is pulling images from pull through proxy server, run following command from your local machine:

```

docker pull alpine

```

This will pull small alpine image. 

Next, check log entries. You'll have to `grep "Trying to pull"` from logs depending on OS you are running. 

```

CentOS 7 - /var/log/messages
Ubuntu 18.04 - /var/log/syslog

```

If everything is working as intended you will see following line which indicates that image has been pulled from `https://registry.domain.tld:5443`

```
Feb 22 14:33:39 local_machine dockerd: time="2021-02-22T14:33:39.618247519+01:00" level=debug msg="Trying to pull alpine from `https://registry.domain.tld:5443/` v2"

```
