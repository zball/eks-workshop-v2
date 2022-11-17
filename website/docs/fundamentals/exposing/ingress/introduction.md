---
title: "Introduction"
sidebar_position: 10
---

:::tip Before you start
Prepare your environment for this section:

```bash timeout=300 wait=30
$ reset-environment 
```

:::

Currently there are no `Ingress` resources in our cluster, which you can check with the following command:

```bash expectError=true
$ kubectl get ingress -n ui
No resources found
```

There are also no `Service` resources of type `LoadBalancer`, which you can confirm with the following command:

```bash
$ kubectl get svc -n ui
NAME   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
ui     ClusterIP   172.20.161.224   <none>        80/TCP    12m
```

The `EXTERNAL-IP` is `<none>`, so this application not currently accessible from outside of the cluster.