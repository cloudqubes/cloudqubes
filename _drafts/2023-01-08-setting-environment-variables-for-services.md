---
layout: post
title:  "How to set proxy for systemd services"
subtitle: > 
  Systemd services will not read the bash environ
date:   2023-01-08 05:17:00 +0530
categories: [recipe]
tags: [linux]
---

Setting the environment variables for  is simple. 

```shell
$ export http_proxy=http://<proxy-ip>:<port>
$ export https_proxy=http://<proxy-ip>:<port>
```


```shell
  151  sudo systemctl set-environment HTTP_PROXY=127.0.0.1:8001
  152  sudo systemctl set-environment HTTPS_PROXY=127.0.0.1:8001
  153  sudo systemctl restart containerd.service

```

```shell
ubuntu@k8s-1:~$ sudo systemctl show-environment
LANG=C.UTF-8
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
HTTPS_PROXY=127.0.0.1:8001
HTTP_PROXY=127.0.0.1:8001
```
