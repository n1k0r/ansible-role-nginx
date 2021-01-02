# Nginx role for Ansible

## Variables

```yaml
domain: domain.name

hosts:
  - primary: domain.name
    aliases:
      - www.domain.name
    default: yes

  - primary: "{{ app.domain }}"
    root: "{{ services_path }}/app/front"
    locations:
      - url: /api
        proxy: "{{ app.internal_port }}"

  - primary: "{{ service.domain }}"
    locations:
      - url: /
        proxy: "{{ service.internal_port }}"
        proxy_ws: yes
      - url: = /robots.txt
        root: "{{ services_path }}/service"
```

Account dictionary is also expected: https://gitlab.com/n1k0r-ansible/roles/master

## Files

Your project folder should contain these files:

```
www/
  dhparam.pem
  first.domain/
  second.domain/
```

Each folder contains static files for corresponding host.
