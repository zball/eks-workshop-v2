---
title: "Compute Tenancy Options"
sidebar_position: 32
sidebar_custom_props: {"module": true}
---

:::tip Before you start
Prepare your environment for this section:

```bash timeout=300 wait=30
$ reset-environment 
```

:::

Multi-tenancy in Kubernetes can come in three forms: 

1. soft multi-tenancy 
2. sole multi-tenancy. 

Soft multi-tenancy is where pods might share the same underlying compute resource (node) and are logically separated using Kubernetes constructs through namespaces, resource quotas, or network policies. A second way to achieve multi-tenancy in Kubernetes is to assign pods to specific nodes that are pre-provisioned and allocated to a specific team. In this case, we talk about sole multi-tenancy. Unless your security posture requires you to use hard or sole multi-tenancy, you would want to consider using soft multi-tenancy for the following reasons:

* Soft multi-tenancy avoids underutilization of resources and waste of compute resources.
* There is a limited number of managed node groups that can be used by Amazon EKS, so for large deployments, this limit can quickly become a limiting factor.
* In sole multi-tenancy there is high chance of ghost nodes with no pods scheduled on them due to misconfiguration as we force pods into dedicated nodes with label, taints and tolerance and anti-affinity rules.



![Insights](/img/multitenancy/tenancyoptions/soft-tenancy.png)
![Insights](/img/multitenancy/tenancyoptions/sole-tenancy.png)
