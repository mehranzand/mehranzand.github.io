---
layout: post
title:  "Moving to Kubernetes"
subtitle: "Actually we need Kubernetes?"
date: 2020-12-03 09:50:00 +000
categories: orchestration
tags: kubernetes docker orchestration
comments: true
author: "Mehran Zand"
meta: "kubernetes ci/cd scaling scale-up scale-out docker"
---
Most applications start their life as a monolith. It is quick and easy to make and deploy changes. But if your application finds success and grows quickly, you will soon need to find ways to scale it. It is time for [Kubernetes] now? Not probably.

### What Does Kubernetes Do?
Kubernetes is an orchestration tool for containerized applications. Starting with a collection of [Docker] containers, Kubernetes can control resource allocation and traffic management for cloud applications and micro-services. It is responsible for:

* Resource balancing containers and clusters
* Managing the scaling of containers and clusters
* Deploying images and containers
* Traffic management for services

### Scaling a Application
Before you decide to split apart your application, there are a number of tactics you can use to scale. Spend a significant amount of time trying to solve your existing problems before making big changes. There are two types of scaling for monolithic and micro-services.

1. **Scale-Up (Vertical way)**
It's very simple in action, this method just adding more hardware resources such as **CPU**, **RAM**, **Disk Space** to the server to handle the requests.

1. **Scale-Out (Horizontal way)**
It refers to adding more instances (**VMs**) to handle the requests. It's a point that driving us to the [Kubernetes].
<br />
![Scaling Types](/assets/2020-12-03-moving-to-kubernetes/scaling.jpg)

### Some Aspects of Kubernetes
1. Kubernetes is a distributed system that needs two machines at least, one machine as main that controls other worker machines.
1. Kubernetes is a complex system with many different services, systems, pieces and its own concepts called [Pods]. Before you can run a single application, you need the following highly-simplified architecture.([Kubernetes documentation])
![A diagram of a Kubernetes cluster and its components](/assets/2020-12-03-moving-to-kubernetes/diagram.jpg)
1. Kubernetes has very efficient management of containers and self healing. Also makes it super easy to establish a proper **CI/CD** pipeline.
1. Understanding Kubernetes is little hard and has a steep learning curve.
1. Local development, Kubernetes does tend to be a bit complicated and unnecessary in environments where all development is done locally.
1. Kubernetes has **Fault Tolerance** feature to handle failure. This ability is magical nearly.

If you do not intend to develop anything complex for a large or with high computing resource needs (e.g. machine learning applications), there is not much benefit for you from the technical power of Kubernetes.
There is no easy answer if adopting Kubernetes is the right choice for you or not, in some situations Kubernetes is a really great idea, Kubernetes might be useful if you need to scale a lot but in others it’s a time-waster with no benefit.


[Kubernetes]: https://kubernetes.io/
[Docker]: https://www.docker.com/
[Kubernetes documentation]: https://kubernetes.io/docs/concepts/overview/components/
[Pods]: https://kubernetes.io/docs/concepts/workloads/pods/
