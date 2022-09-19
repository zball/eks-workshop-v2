---
title: "Configure Priority Expander with CA"
sidebar_position: 10
---

## Assign Priorities to the Node groups 

As part of the initial cluster, we have two nodegroups - `managed-ondemand`(assigned to EKS_DEFAULT_MNG_NAME output variable) and `managed-reserved`(assigned to EKS_PRIORITY_MNG_NAME output variable). For this example, we will assume you have purchased reserved instances and during scaling you want to utilize the reserved instances first. When your cluster scales/expands, the node group for reserved instance should scale first. Only after the `managed-reserved` node group reaches maximum capacity, the other node group for ondemand instances will scale up.

Let's assign priorities to our node groups. The priority should be a positive value. The highest value wins. For each priority value, a list of regular expressions should be given. For the below ConfigMap, node group with the word `reserved` has the highest priority(50), while the node group with the word `ondemand` has lowest priority (10).

```file
autoscaling/compute/priority-expander/setup/configmap.yaml
```

Let's apply this to our cluster:

```bash
$ kubectl apply -k /workspace/modules/autoscaling/compute/priority-expander/setup
```

Verify ConfigMap with the command. It should display the priority of node groups:
```bash
$ kubectl describe configmap cluster-autoscaler-priority-expander -n kube-system
Name:         cluster-autoscaler-priority-expander
Namespace:    kube-system
Labels:       <none>
Annotations:  <none>

Data
====
priorities:
----
10:
  - .*ondemand.*
50:
  - .*reserved.*
```

## Patch Cluster Autoscaler to Use the Priority

By default, Cluster Autoscaler does not use the priorty ConfigMap. You will patch the Cluster Autoscaler to utilize the priorities we configured in the previous step. Run the below command to do so. The last option `--expander=priority` achieves this.

```bash
$ kubectl patch deployment cluster-autoscaler-aws-cluster-autoscaler -n kube-system \
  -p '{"spec": {"template": {"spec": {"containers": [{"name": "cluster-autoscaler-aws-cluster-autoscaler","command": ["./cluster-autoscaler","--v=4","--stderrthreshold=info","--cloud-provider=aws","--skip-nodes-with-local-storage=false","--expander=least-waste","--node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/eks-workshop-cluster","--balance-similar-node-groups","--skip-nodes-with-system-pods=false","--expander=priority"]}]}}}}'
```

## Application Scaling 

Now let us scale the application and see how the node groups scale.

Run the below commands to find out how many EC2s are running in managed-ondemand node group. The output shows there are currently 3 instances running for the managed-ondemand node group, while the max capacity is 6.

```bash
$ aws eks describe-nodegroup --cluster-name $EKS_CLUSTER_NAME --nodegroup-name $EKS_DEFAULT_MNG_NAME --query nodegroup.scalingConfig --output table
---------------------------------------
|          DescribeNodegroup          |
+-------------+-----------+-----------+
| desiredSize |  maxSize  |  minSize  |
+-------------+-----------+-----------+
|  3          |  6        |  3        |
+-------------+-----------+-----------+
```

Run the below commands to find out how many EC2s are running in managed-reserved node group. The output shows there are currently 0 instances running for the managed-reserved node group, while the max capacity is 3.

```bash
$ aws eks describe-nodegroup --cluster-name $EKS_CLUSTER_NAME --nodegroup-name $EKS_PRIORITY_MNG_NAME --query nodegroup.scalingConfig --output table
---------------------------------------
|          DescribeNodegroup          |
+-------------+-----------+-----------+
| desiredSize |  maxSize  |  minSize  |
+-------------+-----------+-----------+
|  0          |  3        |  0        |
+-------------+-----------+-----------+
```

If we deploy and scale our application, initially some pods will get scheduled in the 3 running instances. When those 3 instances fill up, and subsequent pods go to `pending` state, cluster autoscaler will scale up the `managed-reserved` node group first. Once `managed-reserved` node group reaches it's maximum capacity, `managed-ondemand` will start scaling next. 

Lets create and scale the application 

```kustomization
autoscaling/compute/priority-expander/scale/deployment.yaml
Deployment/orders
```

Let's apply this to our cluster:

```bash hook=pa-pod-scaleout timeout=180
$ kubectl apply -k /workspace/modules/autoscaling/compute/priority-expander/scale
```

View the cluster-autoscaler logs

```bash test=false
$ kubectl -n kube-system logs \
  -f deployment/cluster-autoscaler-aws-cluster-autoscaler
```

Check the [EC2 AWS Management Console](https://console.aws.amazon.com/ec2/home?#Instances:sort=instanceId) to confirm that the Auto Scaling groups are scaling up to meet demand. This may take a few minutes. You should see the pods transition from pending to running as nodes are scaled up. 

Run the following. This shows that higher priority nodegroup `managed-reserved` scaled first to it's max size.

```bash
$ aws eks describe-nodegroup --cluster-name $EKS_CLUSTER_NAME --nodegroup-name $EKS_PRIORITY_MNG_NAME --query nodegroup.scalingConfig --output table
---------------------------------------
|          DescribeNodegroup          |
+-------------+-----------+-----------+
| desiredSize |  maxSize  |  minSize  |
+-------------+-----------+-----------+
|  3          |  3        |  0        |
+-------------+-----------+-----------+
```

```bash
$ aws eks describe-nodegroup --cluster-name $EKS_CLUSTER_NAME --nodegroup-name $EKS_DEFAULT_MNG_NAME --query nodegroup.scalingConfig --output table
---------------------------------------
|          DescribeNodegroup          |
+-------------+-----------+-----------+
| desiredSize |  maxSize  |  minSize  |
+-------------+-----------+-----------+
|  3          |  6        |  3        |
+-------------+-----------+-----------+
```

## Conclusion

In this workshop we have shown how to create priority expander for your node groups, and prioritize certain node groups for scaling before others.
