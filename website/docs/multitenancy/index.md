---
title: "Multi Tenancy"
sidebar_position: 5
weight: 30
---

Many organization use diffrerent cluster strategy for their kubernetes applications. Multitenenat EKS clusters is very common to host multiple applications on a single EKS cluser. The main challenge with mutitenancy is how to define security guradrails and sharing resources gracefully among these applications. 

In this module we will create multitenant cluster for 2 teams. Each teams has multiple applications which has specifc requirements. We will see how to handle below following operational scenarios in the cluster. 

1. Creating cluster.
2. Create Name Spaces for Team A & Team B.
3. Soft Tenancy and Sole Tenancy options in Action.
4. Isolated access controls for Team A and Team B.
5. Define Quotas for Team A and Team B.
6. Mutliple application tenancy and sharing resources gracefully.
7. Priority based resource allocation.
8. Cost split.



1. Control plane isolation
    1.1. Namespaces
    1.2. Access Control
    1.3. Quotas
2. Data plane isolation
    2.1 Network Isolation
    2.2 Storage Isolation
    2.3 Node Isolation
    2.4 Quality Of Service
3. Additonal Considerartions
    3.1 API Priority and Fairness

