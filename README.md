# Nginx role for Ansible

## Variables

```yaml
nginx:
  ssl:
    cert: "{{ certbot_cert | format('example.com') }}"
    key: "{{ certbot_key | format('example.com') }}"
    acme_webroot: "/var/www/acme-challenge/"

  auth_lists:
    - name: developers
      users:
        - username: developer1
          password_hash: $2b$12$012345678901234567890uOwS8BfAL92NYwRa7Ld2G.qrpL9qOR.S
        - username: developer2
          password: q1w2e3r4
          salt: "01234567" # md5 requires no more than 8 chars for salt

  hosts:
    - primary: domain.name
      aliases:
        - www.domain.name
      default: yes

    - primary: "{{ app.domain }}"
      auth:
        msg: "Auth required!"
        list: developers
      root: /srv/app/front
      locations:
        - url: /api
          proxy: "{{ app.internal_port }}"

    - primary: "{{ service.domain }}"
      ssl:
        cert: "{{ certbot_cert | format("example2.com") }}"
        key: "{{ certbot_key | format("example2.com") }}"
      template: website.conf.j2 # optional - overrides default host template
      locations:
        - url: /
          auth:
            msg: "Auth required!!1"
            list: developers
          proxy: "{{ service.internal_port }}"
          proxy_ws: yes
        - url: = /robots.txt
          root: /srv/service

    - primary: "{{ another_app.domain }}" # only used for name of config file
      link: /srv/app/server.conf
```

## Files

Your project folder should contain these files:

```
www/
  dhparam.pem
  first.domain/
  second.domain/
```

Each folder contains static files for corresponding host.
