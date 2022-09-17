---
title: "Priority Expander"
sidebar_position: 20
---

## What is Priority Expander with Cluster Autoscaler?

The Kubernetes cluster autoscaler (CA) for AWS configures EC2 auto scaling group (ASG) of the EKS node group to scale nodes in cluster when there are pods pending to be scheduled.

At times, customers want to prioritize certain node group with certain instace types to scale first. Some use cases for this is when customer wants to provision spot instances first, or prioritize reserved instance type(s) to save cost, or prioritize scaling a certain type of EC2s based on workload.

This workshop solves this by **assigning priorities** to each node group. Node groups with higher priority expands (scales) first to their max capacity. Then lower priority node group starts scaling. You can read more about priority expander in the [Priority Expander Kubernetes Documentation](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/expander/priority/readme.md).
