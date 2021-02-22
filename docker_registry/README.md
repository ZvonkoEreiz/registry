docker_registry
=========

Set up Docker pull through registry container.

Requirements
------------

Valid SSL certificate for the registry hostname located in {{ mount_dir }}/certs

Role Variables
--------------

All variables are set up in project root directory group_vars/all as they are used by multiple roles. If you're using this role as a standalone, you can set up following in defaults/main.yml or vars/main.yml:

```

mount_dir: /path/to/some/dir/{{ registry_host }} #Path to directory followed by {{ registry_host }} where you are going to store SSL certificates and Docker's config.yml
registry_host: domain.tld #Hostname for your pull through registry
registry_container_name: some_name #Name for your registry Docker container
registry_image: registry:2.1 #Image for your registry container
host_listening_port: 5443 #Docker host port you are going to map container's port 433 to

```

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

    - hosts: servers
      roles:
         - docker_registry
