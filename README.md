# service-harbor
Service harbor via Ansible


## Run playbook

```bash
ansible-playbook -K -i inventory main.yml
```

Role Name
=========

Harbor. A collection of tools which provide a docker+helm registry with image scanning and content trust. Inspired by v1.5.1 tag of <https://github.com/goharbor/harbor-helm/>

Requirements
------------

Role Variables
--------------

Updates/LCM can be done by changing the tag version in the defaults/main.yml variable file.

```yaml
# Portal
  portal:
    fqdn: "{{ harbor_portal_name }}.{{ basedomain }}"
    image:
      repository: goharbor/harbor-portal
      tag: v2.1.3
```

The harbor url can be changed via:

```yaml
harbor_portal_name: harbor<name>
```

Dependencies
------------

Example Playbook
----------------

Define variables to create a general admin users:

```yaml
customerusers:
  - email: appfactory@kpn.com
    firstname: jan
    k8sroles: '{{ afd0_harbor_admin }}'
    lastname: janssen
    password: '{SSHA}bqlPTgd5NYXEx+csHR54Kmv3sfQ3zPg5'
    user: jjanssen

afd0_harbor_admin:
  - harbor-users
  - harbor-admin
```

Extra LDAP groups can be defined with:

```yaml
harbor:
  projects:
    project1:
      public: "false"
    project2:
      public: "true"
  groups:
    - project1-harbor-admin
    - project1-harbor-maintainer
```

Get the Harbor password:

```bash
kubectl get secret harbor-users -o jsonpath='{.data.admin}' | base64 -d
```

If Mod Security is needed, they can be added via Appfactory settings as seen below.
Extra Nginx annotations can be added as well: `nginx.ingress.kubernetes.io/extra_annotation: "test annotation"`

```yml
harbor:
  ingress_annotations:
    nginx.ingress.kubernetes.io/modsecurity-snippet: |
      SecRuleEngine On
      SecRule REQUEST_URI "^/api/v2.0/users/.*/password$" "phase:2,nolog,pass,id:10014,ctl:ruleRemoveById=949110"
      SecRule REQUEST_URI "^/api/v2.0/projects" "phase:2,nolog,pass,id:10015,ctl:ruleRemoveById=949110"
      SecRule REQUEST_URI "^/api/v2.0/configurations$" "phase:2,nolog,pass,id:10016,ctl:ruleRemoveById=949110"
      SecRule REQUEST_URI "^/v2" "phase:2,nolog,pass,id:10017,ctl:ruleRemoveById=949110"
    nginx.ingress.kubernetes.io/extra_annotation: "test annotation"
```

License
-------

BSD

Author Information
------------------
