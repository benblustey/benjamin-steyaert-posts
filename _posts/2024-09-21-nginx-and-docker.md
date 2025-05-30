---
title: NGINX & Docker
date: 2024-09-21
categories: webdev,
tags:
  - docker
  - nginx
  - networking
image: /assets/img/posts/nginx-docker.jpg
---
For my link tree landing page, I decided to host on my DigitalOcean droplet which hosts multiple projects, websites, endpoints for offsite logs, and an Uptime service to alert me of issues accessing my homelab. The stack includes Docker, Traefik V3, LetsEncrypt, Cloudflare and simple NGINX for hosting static websites. When building and debugging my configuration, I learned a couple things from encountering some obstacles that should be documented. 
## Reload Nginx
When changing the configuration files inside the nginx container, or from the mounted files, restarting the nginx service is necessary to load the changes. This is how without any downtime and without interrupting and connections.

Reload the nginx service without restarting the container.
```sh
$ docker exec -it nginx-server nginx -s reload
```
From with in the container shell:
```sh
$ docker exec -t nginx-server sh
$ nginx -s reload
```
## Mount Log Files
By default Nginx image output the logs **access/error** to **stdout/stderr**. This is done to output the logs into the command line for "proper" way of logging containers. For my debugging, I wanted the files mounted locally when testing. 
From within the container:
```sh
$ rm -f /var/log/nginx/*
# then restart the nginx service
$ nginx -s reload
```
