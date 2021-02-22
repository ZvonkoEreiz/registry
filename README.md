# registry

Project contains roles designed to set up Docker pull through caching registry container. 
Project is fragmented into three roles which can be run as a standalone depending on your current needs. 

### Brief explanation of roles listed in setup.yml
- certbot installs Certbot and SSL certificate for your domain.
- docker_install installs Docker from the official repository on Centos systems (It can also be applied on later Fedora systems and both Centos 7 and 8).
- docker_registry sets up Docker pull through registry container.

### Variables defaults
All variables are set up in group_vars/all as most are used by multiple roles. 
If you're using role as a standalone, you can set up variables in $role/defaults/main.yml or $role/vars/main.yml as describes in role's specific README.md files.

```

mount_dir: "/etc/docker/{{ registry_host }}" #path to directory where SSL certificates and Docker config file for your registry will be stored
registry_host: registry2.zvonkotest.online #hostname for your registry
ssl_email: zvonkoereizzg@gmail.com #administrative email address for your SSL certificates
registry_container_name: registry #name of your registry container; best practice would be to use the same one as registry hostname
registry_image: registry:2.1 #image for your registry container
host_listening_port: 5443 #Docker host port you are going to map container's port 433 to

```

