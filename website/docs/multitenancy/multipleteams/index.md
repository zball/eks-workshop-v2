---
title: "Multiple Teams"
sidebar_position: 20
sidebar_custom_props: {"module": false}
---

:::tip Before you start
Prepare your environment for this section:

```bash timeout=300 wait=30
$ reset-environment 
```

:::
When multiple teams sharing the same cluster, each team needs completes isolation in terms of security, cost and resource utilization. Namespaces provides completes isolation for teams while operating in the single cluster.By default, A cluster created with default namespace. 

In this lab we create 2 Namespaces for each teams. 

```bash timeout=300 wait=30
$ kubectl create ns team-a
```
```bash timeout=300 wait=30
$ kubectl create ns team-b
```