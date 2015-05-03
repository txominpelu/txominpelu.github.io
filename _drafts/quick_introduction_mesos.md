---
layout: post
title:  Quick introduction to Mesos
---

# What is Mesos?

Mesos is a *cluster resource manager*. To put it simple, it allows multiple
applications to run in the same cluster, 

  1. **isolating** every application from others

  2. **protecting** them against
conflicting uses of resources. 

  3. providing a level of **abstraction** with a set of functionalities that
  facilitate the development of distributed applications.


Many popular frameworks like Spark and Storm or applications like Aurora or
Marathon, Chronos and etcd use Mesos as a platform. Chances are that there is
already a framework for your "distributed needs" that runs on Mesos.

# What does it offer?

More concretely Mesos provides four groups of functionalities that prove really
useful when building distributed systems:

  1. Resource Management
  2. Isolation
  3. Resilience: Failover and High Availability 
  4. Abstraction: Simple, event/offer-based scheduling for applications +
  intra-framework message exchange.


