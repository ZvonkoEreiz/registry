certbot
=========

Install Certbot and SSL certificate for the domain. Additionally, it installs Python module pexepct required to autofill certificate request.

Requirements
------------

Domain with DNS pointing to the server due to DNS verification required by Certbot

Role Variables
--------------

All variables are set up in project root directory group_vars/all as they are used by multiple roles. If you're using this role as standalone, you can set up following in defaults/main.yml or vars/main.yml:

```
ssl_email: user@domain.tld #administrative email address for your SSL certificates
registry_host: domain.tld #name of the domain you'd like to install SSL certificate on
mount_dir: /path/to/some/directory #path to directory where you'd like to copy your SSL certificate

```

Dependencies
------------

None.

Example Playbook
----------------

    - hosts: servers
      roles:
         - certbot
