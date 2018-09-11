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

Run with `--skip-tags deps` to skip installing dependency roles.

### Remote Node

- [Docker][]  (For certbot-config)
- [docker-py][]

[Docker]: https://www.docker.com
[docker-py]: https://docker-py.readthedocs.io


Role Variables
--------------

### Global Vars

```yaml
config_dir:          "/config"
common_name:         "example.com"
public_host_name:    "www"
admin_email:         "admin@{{ secured_common_name }}"
```

### Certificate Issuer

`cert_type` may be one of:

- `selfsigned`
- `letsencrypt` -- uses uses Ansible's Let's-Encrypt package, appears to be _broken_
- `certbot` -- uses certbot's Docker image
- `local` -- tries to run certbot locally and copy certs to remote (for wildcards, may be _broken_)

As with most of the `derek.merck` roles, the role respects a number of common global settings that may be set in the playbook or inventory.

```yaml
cert_type:           "selfsigned"
do_api_tok:          False
```

### Certbot Configuration

Set the challenge type:

- `standalone` - simple, but no other server may be using port 80
- `webroot` - a bit of a pain to integrate with an existing server since it requires a challenge path redirect
- `do-dns` (DigitalOcean dns) is easiest; it does not manipulate the local server settings, but you must get a token and define the `do_api_tok` variable in the playbook or inventory.

Defaults to the staging server (which will still be considered insecure, but has loose restrictions on requests for testing).  Set the `staging` flag to False for production (and possibly set the `force` flag to True if certbot complains about switching away from a valid staging cert).

```yaml
secured_cb_challenge: "standalone"
secured_cb_staging: True
secured_cb_force:   False
```

Example Playbook
----------------

```yaml
- hosts: internet_servers
  tasks:
     - include_role:
         name: derekmerck.secured
       vars:
         common_name:     "example.com"
         cert_type:       "certbot"
         secured_cb_type: "standalone"
```


License
-------

MIT