---
layout: post
title:  "Kubernetes networking explined"
subtitle: > 
  Introduce the concept of Kubernetes Ingress.
date:   2022-12-28 11:00:00 +0530
categories: [devops, tutorial]
tags: [Kubernetes]
---

Kubernetes networking is one of the most importatn concpets that you must grasp. Especially, if you are coming from a virtualization backgroud kubernetes networking can surpise you.

Kubernetes does not have a virtual networking model. It adopts a flat networking model.

image - 3 nodes. 6 pods. all pods can communicate directy with each other.

This flat networking model is very efficient for the applications running on K8s. any Pod can just talk to any other pd without SNAT etc.

# Linux namespaces

Linux namespaces is a basic building block of containers. each container runs in a separate namespace to isolate its resources from the host system and from the other containers running on the same host. 

When a conatiner is created a new network namespace is created and the Pod gets an IP address in the new namepsace.
The physical network device resides in the default namespace created by `init`. The pod running in a different namespace does not have access to this physical device.

So, the cotainer runtime creates the veth devices that bridge the namespace to the default namesapce. 

Therefore, while any pod can directly communicate with anyother pod, it cannot communicate with an external node that does not belong to the cluster even if the node resides on the same subnet as the cluster.
Also, for the same reason any application that is outside the cluster cannot directly talk to the Pods. K8s implements Kubernetes Services for satisfying this requirement.

# Netorking lougins
K8s does not implement this networking. It leaves out networking to CNIs. 
There are several types of 