---
layout: post
title:  "Gitlab runners and docker-compose"
date:   2020-01-19 20:50:26 +1100
categories: gitlab docker
---

{%- highlight ini -%}
  [req]
  prompt             = no
  default_bits       = 2048
  x509_extensions    = v3_req
  distinguished_name = req_distinguished_name

  [req_distinguished_name]
  organizationName        = qcu87z
  commonName              = 192.168.1.25

  [v3_req]
  subjectAltName = @alt_names

  [alt_names]
  IP.1 = 192.168.1.25

{%- endhighlight -%}


Regenerate certs

{% highlight shell %}
  openssl req -x509 -days 1000 -nodes -out 192.168.1.25.crt -keyout 192.168.1.25.key -config ssl.cnf -extensions v3_req
{% endhighlight %}

### Docker compose file

{%- highlight yaml -%}
  version: '3.5'
  services:
    web:
      image: 'gitlab/gitlab-ce:latest'
      restart: always
      hostname: '192.168.1.25'
      environment:
        GITLAB_OMNIBUS_CONFIG: |
          external_url 'https://192.168.1.25'
          # Add any other gitlab.rb configuration here, each on its own line
      ports:
        - '80:80'
        - '443:443'
        - '22:22'
      volumes:
        - '/srv/gitlab/config:/etc/gitlab'
        - '/srv/gitlab/logs:/var/log/gitlab'
        - '/srv/gitlab/data:/var/opt/gitlab'
      networks:
        - gitlab

    runner:
      image: gitlab/gitlab-runner:alpine
      restart: unless-stopped
      depends_on:
        - web
      volumes:
        - '/srv/gitlab-runner:/etc/gitlab-runner'
        - '/var/run/docker.sock:/var/run/docker.sock'
      networks:
        - gitlab

  networks:
    gitlab:

{%- endhighlight -%}