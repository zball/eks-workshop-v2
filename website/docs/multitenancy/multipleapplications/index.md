---
title: "Multiple Applications"
sidebar_position: 20
sidebar_custom_props: {"module": true}
---

:::tip Before you start
Prepare your environment for this section:

```bash timeout=300 wait=30
$ reset-environment 
```

:::

When multiple applications sharing the same cluster, each application needs complete isolation in terms of security, cost and resource utilization. 

In this lab we create 2 Namespaces for each application. 

```bash timeout=300 wait=30
$ kubectl create ns app-a
```
```bash timeout=300 wait=30
$ kubectl create ns app-b
```

