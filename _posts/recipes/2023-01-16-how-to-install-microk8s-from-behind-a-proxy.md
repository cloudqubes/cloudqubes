---
layout: post
title:  "How to install MicroK8s behind a web proxy"
subtitle: > 
  You must set the proxy for snap and systemd services, or the installation will fail.
date:   2023-01-16 14:30:00 +0530
categories: [recipe]
tags: [linux]
---

[MicroK8s] is great for trying out Kubernetes on your laptop or a virtual machine. It is available as a [snap] package so the installation process is simple.

However, MicroK8s assumes that your host computer has direct access to the Internet. That's fine for a laptop. But, if you are installing MicroK8s inside a private data center, Internet access will be available only via a web proxy.

In that case, make sure to follow these two steps before installing MicroK8s.

## #1: Set the proxy server for snap

The `snap` package manager reads proxy configurations from its system options.

```shell
$ sudo snap set system proxy.http="http://1.2.3.4:9876"
$ sudo snap set system proxy.https="http://1.2.3.4:9876"
```

Note that `1.2.3.4` is the proxy IP address and `9876` is the proxy port. Replace them with the actual values of your proxy server.

Use this format for proxy servers that require authentication.

```shell
$ sudo snap set system proxy.http="http://username:password@1.2.3.4:9876"
$ sudo snap set system proxy.https="http://username:password@1.2.3.4:9876"
```

<div class="alert-highlight">
<span class="material-symbols-outlined">warning</span>
<span class="alert-text">Username and password would be logged in the Linux command history.</span>
</div>

Previously, I have experienced problems when the password contains `#` character. It is not confirmed. But, be mindful of any special characters in the password.

## #2: Set the proxy server for services

`kubelite` which is a part of MicroK8s downloads several container images from the Internet during the installation process. Since `kubelite` runs as a Systemd service, you must configure the proxy server for Linux services.

```shell
$ sudo systemctl set-environment no_proxy=10.0.0.0/8,192.168.0.0/16,127.0.0.1,172.16.0.0/16
$ sudo systemctl set-environment https_proxy=http://1.2.3.4:9876
$ sudo systemctl set-environment http_proxy=http://1.2.3.4:9876
```

Remember to use the correct values for the IP address and the port. Also, if the proxy required authentication, use the string `http://username:password@1.2.3.4:9876`.

The variable `no_proxy` is important. It instructs the system *not* to use proxy for specific IP addresses - private IP ranges and the localhost `127.0.0.1` in this case.

Why do we need to set this?

MicroK8s consists of five Systemd services that are listening on different ports in `127.0.0.1`.
```shell
$ sudo systemctl list-units --type=service | grep micro
  snap.microk8s.daemon-apiserver-kicker.service  loaded active running Service for snap application microk8s.daemon-apiserver-kicker                
  snap.microk8s.daemon-cluster-agent.service     loaded active running Service for snap application microk8s.daemon-cluster-agent                   
  snap.microk8s.daemon-containerd.service        loaded active running Service for snap application microk8s.daemon-containerd                      
  snap.microk8s.daemon-k8s-dqlite.service        loaded active running Service for snap application microk8s.daemon-k8s-dqlite                      
  snap.microk8s.daemon-kubelite.service          loaded active running Service for snap application microk8s.daemon-kubelite                        
```

These services must be able to communicate via HTTP APIs on their destined ports for MicroK8s to work. If `no_proxy` is not set, those API calls would also be sent to the proxy server and will get failed. Therefore, MicroK8s would not be set up correctly if `no_proxy` is not configured.


[snap]: https://snapcraft.io/microk8s
[MicroK8s]: https://microk8s.io
