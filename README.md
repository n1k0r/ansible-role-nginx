# Nginx role for Ansible

## Variables

```yaml
domain: domain.name

nginx:
  ssl:
    cert: "{{ certbot_cert }}"
    key: "{{ certbot_key }}"

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
      root: "{{ services_path }}/app/front"
      locations:
        - url: /api
          proxy: "{{ app.internal_port }}"

    - primary: "{{ service.domain }}"
      locations:
        - url: /
          auth:
            msg: "Auth required!!1"
            list: developers
          proxy: "{{ service.internal_port }}"
          proxy_ws: yes
        - url: = /robots.txt
          root: "{{ services_path }}/service"
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
