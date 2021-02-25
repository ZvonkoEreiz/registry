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

## Local machine instructions

### Linux

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

CentOS 7 and 8 - /var/log/messages
Ubuntu 18.04 and 20.04 - /var/log/syslog

```

For Fedora 32 and 33 use:

```

journalctl -u docker | grep "Trying to pull"

```


If everything is working as intended you will see following line which indicates that image has been pulled from `https://registry.domain.tld:5443`

```
Feb 22 14:33:39 local_machine dockerd: time="2021-02-22T14:33:39.618247519+01:00" level=debug msg="Trying to pull alpine from https://registry.domain.tld:5443/ v2"

```

However, if you see following line, it indicates that image was pulled from docker.io and you'll have to set up /etc/docker/daemon.json once again and restart docker on your local machine

```

Feb 22 18:41:50 local_machine dockerd[3919]: time="2021-02-22T18:41:50.536343645+01:00" level=debug msg="Trying to pull alpine from https://registry-1.docker.io v2"

```

### OSX

Open your Docker GUI and switch to Settings > Docker Engine. Here you can edit you daemon.json file by adding following to it with registry.domain.tld:5443 being registry hostname and Docker host port:

```

  "registry-mirrors": [
    "https://registry.domain.tld:5443"
  ],
  
  ```
  
  If there is any issue with the code you'll see message written in red that looks like:
  
  ```
  
  Unexpected token a in JSON at position 1
  
  ```
  
If everything looks fine click on "Apply & Restart" button. 

Next step is to check if Docker is pulling images from proxy server. To do that, open two tabs in your terminal. In the first tab run following two commands:

```

pred='process matches ".*(ocker|vpnkit).*"
  || (process in {"taskgated-helper", "launchservicesd", "kernel"} && eventMessage contains[c] "docker")'
  
/usr/bin/log stream --style syslog --level=debug --color=always --predicate "$pred"

```

In your second terminal tab, run docker pull alpine (or any other image you'd like to pull) and you should see some of following lines in your first terminal tab:

```

2021-02-25 15:32:07.133445+0100  localhost com.docker.vpnkit[14873]: HTTP proxy --> 10.10.10.10:5443 Host:registry.domain.tld:5443 (Origin): Successfully connected to 10.10.10.10:5443

```

This indicates that your Docker engine is trying to pull image from proxy server registry.domain.tld:5443, however, please keep in mind that if you misspell image name or if set up on your local machine was not applied correctly, you will also see following lines which indicate pull from Docker registry server registry-1.docker.io:443:

```

2021-02-25 15:28:20.874663+0100  localhost com.docker.vpnkit[14873]: HTTP proxy --> 52.1.121.53:443 Host:registry-1.docker.io:443 (Origin): CONNECT
2021-02-25 15:28:21.010527+0100  localhost com.docker.vpnkit[14873]: HTTP proxy --> 52.1.121.53:443 Host:registry-1.docker.io:443 (Origin): Successfully connected to 52.1.121.53:443

```

If this happens, please check image name and that proxy server hostname has been spelled correctly and that changes have been applied. 
