---
layout: post
title:  "What is Kubernetes Ingress"
subtitle: > 
  Introduce the concept of Kubernetes Ingress.
date:   2022-12-28 11:00:00 +0530
categories: [devops, tutorial]
tags: [Kubernetes]
---

Kubernetes Ingress implements HTTP access to services running in a Kubernetes cluster.

# Why use Kubernetes Ingress

Kubernetes is a platform for running containerized applications. You run containers in Kubernetes Pods. A Pod is assigned an IP address that is unique to the cluster. This IP address is reachable only within the cluster since Kubernetes does not implement any networking features to advertise Pod IP addresses to any outside entity.

Therefore, an application you deploy in Kubernetes is not directly accessoble to clients outside the cluster. To expose your application to outside clients you can ise Kubernetes Ingress. 

However, Kubernetes Ingress supports only HTTP and HTTPs protocols. If your application needs to be accessible to outside parties using any other protocol you must look into other methods.

# Ingress and ingress Controller

Ingress is an API object in Kubernetes. It defines rules on how an application must be connected, but Ingress does not execute any actions. 

An Ingress Controller is a software application that runs inside the Kubernetes cluster and implements the rules defined in an Ingress object. The Ingress Controller is responsible for routing HTTP messages to destined services within the cluster.

# How Kubernetes Ingress works

The scope of Kubernetes is to schedule Pods into Nodes in a cluster. Inside the cluster, networking is implemented by networking plugins. External networking is not handled by plugins because networking outside the cluster depends on where a particular Kubernetes cluster is deployed.

To allow an external client to access an application there must be an IP address that is accessbile from outside the cluster. 


Kubernetes does not care about networking. 

Internal networking in Kubernetes is handled by Network plugins. External networking is not hadleds by plugins because external networking depends on the architecture of your Kubernetes cluster.

If you run Kubernetes in an on-premise data ceneter, you would have a set of hosts connected to a data center network that is connected to a DCGW and corporate MPLS network.

Running Kubernetes on public cloud you will have a set of virtual nodes within a virtual private cloud. This virtual cloud would be connected to the Internet via an internet Gateway. 

If you are running Kubernetes on a single VM in your laptop, you will be connected to a virtual NIC. 

How you should connect to the outside world is different in these three scenarios. 

Kubernetes Ingress let you abstract this underlying networking architecture and let you route traffic to the application running inside the Kubernetes cluster.

However, the Ingress controller is unaware about the underlying networking architecture. So you would need a loadbalancer for routing the IP packets to the Nginx controller.

# Ingress controller vs Loadbalancer Services