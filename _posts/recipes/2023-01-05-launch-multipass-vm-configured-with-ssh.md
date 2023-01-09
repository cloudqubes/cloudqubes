---
layout: post
title:  "How to launch Multipass VMs configured with SSH authentication"
subtitle: > 
  Using cloud-init to configure SSH key-based authentication in Multipass VMs.
date:   2023-01-05 04:00:00 +0530
categories: [recipe]
tags: [linux]
---

Multipass is your friend for quickly spinning up virtual machines in Linux, Mac, or Windows.
Just install Multipass and run `multipass launch --name my-vm` to create a VM with the latest Ubuntu LTS release. 

SSH authentication is disabled in this. You must access it with `multipass shell my-vm` and then configure SSH authentication.

That's too much work. So, let's use [cloud-init] to cofigure key-based authentication at the VM launch.

### Create the SSH keys

Create a new key pair in the host machine where [Multipass] is running .

```shell
$ ssh-keygen -f .ssh/multipass_vm_key
```

Print the public key.
```shell
$ cat .ssh/multipass_vm_key.pub 
```
Then, copy the output to clipboard.


### Create the user data file for cloud-init

Create a new file `vm_config.yml`.

```yaml
#cloud-config
users:
  - name: ubuntu
    ssh_authorized_keys:
      ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDbGROILWv5VKu+03TlI9cdZOSZHuXtIfoH32pA0F4C88nRI05IJ+N2VlvSyaP6Zf9UdRHZfa7bkNFbvOQESvir4DgaNRS4aUaSApV13jrr7JSgM6hGMJf32gnUXCUXntXDT+MbJ8PALfCQNRvTcv1ZcQbDBGEhuAwovlzwXOrKoqc3f51Hhsi1nEt/GOm1lQ4shMxkxhM5e3ontC+2cbvwKcgR0k29maJNWi7HnO69dCHhxTlvXsLLN9dUeubXEg6oEMBeIvjKOb2UHk5N1O4zvFdfNGnM1Kf0ef3KUIAZh5rNa/Fq4/QRR75BJ/uF4nuOB1F4ZKmDXPEcoVajTBPLLvhIPzukipKi4BxJBjpibnv6w3tC9M/708UYuRLX2jRyNvQJ52ntlOCATXrq1W4KpS6s7hLBBd4C0gG4wACrmhm7PqVntl9whsBpJcyBWB5T3R2Ce4MzOQYzh+XhmX+CprnuCsoBJv+KleDRLVklyLvOWu5UOYczJBB7mWqTYZs= ubuntu@my_host
```
Replace the line beginning wth `ssh-rsa` with the content copied to clipboard in the previous step.

### Launch VM

Use the created yml file and launch a VM.

```shell
$ multipass launch --cloud-init vm_config.yml -n my-vm
```

Check the IP address
```shell
$ multipass list                          
Name                    State             IPv4             Image
my-vm                   Running           10.221.207.193   Not Available
```

Login to the new VM with SSH.


```shell
$ ssh -i .ssh/multipass_vm_key ubuntu@10.221.207.193
```

[cloud-init] is not limited to enabling SSH. It supports doing a variety of configurations at the initialization or boot-time of in cloud-based vitual machines.

[Read the docs] to check out more features.

[Multipass]: https://multipass.run/
[cloud-init]: https://cloud-init.io/
[Read the docs]: https://cloudinit.readthedocs.io/en/latest/
