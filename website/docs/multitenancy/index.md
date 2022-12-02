---
title: "Multi Tenancy"
sidebar_position: 5
weight: 30
---

Many organization use diffrerent cluster strategy for their kubernetes applications. Multitenenat EKS clusters is very common to host multiple applications on a single EKS cluser. The main challenge with mutitenancy is how to define security guradrails and sharing resources gracefully among these applications. 

In this module we will cover how you can use EKS clsuter with multi tenants with following features:

1. Control plane isolation
    1.1 Namespaces
    1.2 Access Control
    1.3 Quotas
2. Data plane isolation
    2.1 Network Isolation
    2.2 Storage Isolation
    2.3 Node Isolation
    2.4 Quality Of Service
3. Additonal Considerartions
    3.1 API Priority and Fairness

