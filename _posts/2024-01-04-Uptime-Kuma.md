---
title: "Update Kuma"
date: 2024-01-04 21:45:00 -700
categories: [Uptime Kuma]
tags: [homelab,proxmox,update kuma]
---

# Uptime Kuma

Uptime kuma is great app for monitoring your netwok service and websites. i followed the NetworkChuck video for [Uptime Kuma](https://www.youtube.com/watch?v=DbF96IHOZig&t=263s)

It is running in a docker container and was created using Docker compose. 

## Updating Uptime Kuma
```shell
cd "<YOUR docker-compose.yml DIRECTORY>"
docker-compose pull
docker-compose up -d --force-recreate
```
