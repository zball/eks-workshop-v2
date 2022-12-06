---
title: "Resource Sharing with Guardrails"
sidebar_position: 34
sidebar_custom_props: {"module": true}
---

:::tip Before you start
Prepare your environment for this section:

```bash timeout=300 wait=30
$ reset-environment 
```

:::

1. Resource Quota
2. Limit Range

Resource Quotas

When several users or teams share a cluster with a fixed number of nodes, there is a concern that one team could use more than its fair share of resources. Resource quotas are a tool for administrators to address this concern. A resource quota, defined by a ResourceQuota object, provides constraints that limit aggregate resource consumption per namespace. It can limit the quantity of objects that can be created in a namespace by type, as well as the total amount of compute resources that may be consumed by resources in that namespace.

Resource quotas work like this:

Different teams work in different namespaces. This can be enforced with RBAC.

The administrator creates one ResourceQuota for each namespace.

Users create resources (pods, services, etc.) in the namespace, and the quota system tracks usage to ensure it does not exceed hard resource limits defined in a ResourceQuota.

If creating or updating a resource violates a quota constraint, the request will fail with HTTP status code 403 FORBIDDEN with a message explaining the constraint that would have been violated.

If quota is enabled in a namespace for compute resources like cpu and memory, users must specify requests or limits for those values; otherwise, the quota system may reject pod creation. Hint: Use the LimitRanger admission controller to force defaults for pods that make no compute resource requirements.

Limit Ranges

By default, containers run with unbounded compute resources on a Kubernetes cluster. Using Kubernetes resource quotas, administrators (also termed cluster operators) can restrict consumption and creation of cluster resources (such as CPU time, memory, and persistent storage) within a specified namespace. Within a namespace, a Pod can consume as much CPU and memory as is allowed by the ResourceQuotas that apply to that namespace. As a cluster operator, or as a namespace-level administrator, you might also be concerned about making sure that a single object cannot monopolize all available resources within a namespace.

A LimitRange is a policy to constrain the resource allocations (limits and requests) that you can specify for each applicable object kind (such as Pod or PersistentVolumeClaim) in a namespace.

A LimitRange provides constraints that can:

Enforce minimum and maximum compute resources usage per Pod or Container in a namespace.
Enforce minimum and maximum storage request per PersistentVolumeClaim in a namespace.
Enforce a ratio between request and limit for a resource in a namespace.
Set default request/limit for compute resources in a namespace and automatically inject them to Containers at runtime.
A LimitRange is enforced in a particular namespace when there is a LimitRange object in that namespace.

Constraints on resource limits and requests:

The administrator creates a LimitRange in a namespace.

Users create (or try to create) objects in that namespace, such as Pods or PersistentVolumeClaims.

First, the LimitRange admission controller applies default request and limit values for all Pods (and their containers) that do not set compute resource requirements.

Second, the LimitRange tracks usage to ensure it does not exceed resource minimum, maximum and ratio defined in any LimitRange present in the namespace.

If you attempt to create or update an object (Pod or PersistentVolumeClaim) that violates a LimitRange constraint, your request to the API server will fail with an HTTP status code 403 Forbidden and a message explaining the constraint that has been violated.

If you add a LimitRange in a namespace that applies to compute-related resources such as cpu and memory, you must specify requests or limits for those values. Otherwise, the system may reject Pod creation. LimitRange validations occur only at Pod admission stage, not on running Pods. 7. If you add or modify a LimitRange, the Pods that already exist in that namespace continue unchanged.

If two or more LimitRange objects exist in the namespace, it is not deterministic which default value will be applied.

      ---------------------------------------------
So if you try to summarise ResourceQuota applies restriction with respect to CPU, memory for workloads and no of objects that can be created in a namespace. LimitRange defines default, maximum, minimum of CPU and memory consumption by workloads in a namespace. If you have a quota applied in a namespace, every Pod must request for resources like CPU and memory in their manifests. Otherwise the Pod creation will fail. But if you have a LimitRange enforced with default memory and CPU requests that could be avoided.