Ansible Role for Issuing SSL Certs
==================================

Derek Merck  
<derek_merck@brown.edu>  
Rhode Island Hospital and Brown University  
Providence, RI  

Configure and create SSL certificates for nginx.


Dependencies
------------

### Galaxy Roles

- [geerlingguy.docker](https://github.com/geerlingguy/ansible-role-docker) to setup the docker environment
- [geerlingguy.pip](https://github.com/geerlingguy/ansible-role-pip) to install Python reqs


### Remote Node

- [Docker][]  (For certbot-config)
- [docker-py][]

[Docker]: https://www.docker.com
[docker-py]: https://docker-py.readthedocs.io


Role Variables
--------------

### Certificate Issuer

`secured_cert_type` may be one of "selfsigned", "letsencrypt", or "certbot".  "letsencrypt" uses Ansible's Let's-Encrypt package.  The server _must_ be running to handle the letsencrypt challenge.  If no certs currently exist, the server will have to run under self-signed or unsecured first.

```yaml
secured_cert_type:      "selfsigned"
```

### Other vars

```yaml
# does not work with extended chars like 'zmø.biz'
secured_common_name:    "example.com"
secured_host_name:      "www.{{ secured_common_name }}"

secured_admin_email:    "admin@{{ secured_common_name }}"
secured_org_name:       ""
```

Example Playbook
----------------

Bring up a fresh server and get new certficates

```yaml
- hosts: web_server
  tasks:
     - include_role:
         name: derekmerck.secured
       vars:
         secured_cert_type: self-signed

     - include_role:
         name: derekmerck.nginx
       vars:
         nginx_container_name: nginx
         nginx_secured: True

     - include_role:
         name: derekmerck.secured
       vars:
         secured_cert_type: letsencrypt

     - docker_container:
         name: nginx
         state: started
         restart: True
```


License
-------

MIT