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

Ingress is an API object in Kubernetes. You create an ingress object to connect an application to the outside world. Ingress defines rules on how an application must be connected, but Ingress does not execute any actions. 

<div class="inline-highlight">
Ingress is an object stored in Kubernetes.
Ingress controller is a software that runs in a Kubernetes cluster.
</div>

An Ingress Controller is a software application that runs inside the Kubernetes cluster and implements the rules defined in an Ingress object. The Ingress Controller is responsible for routing HTTP messages to destined services within the cluster.

# Ingress Controllers

The notion `ingress controller` may imply that the ingress controller is a part of the Kubernetes control plane. But, it is not. You must run the ingress controller in a Pod in one or more worker nodes. 

Also, ingress controller is not a part of core Kubernetes software. There are many [ingress controllers available].

Some of them like the [aws-loadbalancer-controller] are avalable on public clouds, and others like [nginx] are open-source projects that can be installed in private data centers.

[nginx] is an officially supported ingress controller maintained by the Kubernetes project. It is available for free. There is also another ingress controller named as [NGINX Ingress Controller] which is a commercial product from the NGINX company.

# Installing an Ingress controller

Since the ingress controller is not a part of Kubernetes software, it must be separately installed in a Kubernetes cluster.

When using a Kubernetes service from any cloud provider, an ingress controller would be installed and managed by the cloud provider. 

In a private data center, you can use the free [nginx] ingress controller or any of the commercial solutions available. 

The installation instructinos of the [nginx] ingress controller are [here][nginx-installation].


# Ingress helps you abstract the connectivity

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

# How Ingress works

The easiest way to understand about Ingress controller is to think of it as a reverse proxy configured by the Kubernetes control plane. 

Here's how you use the ingress to expose your application to outside world.

1. Create the Kubernetes workload and the service.
2. Create the Kubernetes Ingress
The Kubernetes Ingress is an API object that you create using a manifest file and `kubectl`.
This manifest creates an ingress object for routing traffic to `number-crunch-service` on port 8080. Since Kubernetes abstract the ingress controller implementation you can use the same manifet with different ingress controllers.
3. Kubernetes configures the ingress controller
When you create the ingress object, the Kubernetes control plane will configure the ingress controller how to route the traffic to the specified services.
4. Send traffic

# Ingress controller vs Loadbalancer Services

[ingress controllers available]: https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/
[aws-loadbalancer-controller]: https://github.com/kubernetes-sigs/aws-load-balancer-controller#readme
[nginx]: https://kubernetes.github.io/ingress-nginx/deploy/
[NGINX Ingress Controller]: https://www.nginx.com/products/nginx-ingress-controller/
[nginx-installation]: https://kubernetes.github.io/ingress-nginx/deploy/#quick-start