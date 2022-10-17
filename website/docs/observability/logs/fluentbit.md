---
title: "Install Fluent Bit"
sidebar_position: 5
weight: 35
---

[Fluent Bit](https://fluentbit.io/) is a lightweight log processor and forwarder that allows you to collect data and logs from different sources, unify them and send them to multiple destinations.

For Kubernetes cluster components that run in pods, these write to files inside the <i>/var/log</i>directory, bypassing the default logging mechanism. We can implement cluster-level logging by including a node-level logging agent on each node, such as Fluent Bit. The logging agent collects logs and pushes to a centralized log storage service (Cloudwatch logs). Commonly, the logging agent is a container that has access to a directory with log files from all of the application containers on that node. Because the logging agent must run on every node, it is recommended to run the agent as a DaemonSet

In the following steps, you set up Fluent Bit as a daemonSet to send logs to CloudWatch Logs.

To install Fluent Bit to send logs from containers to CloudWatch Logs;
1. If you don't already have a namespace called amazon-cloudwatch, create one by entering the following command:
```bash
kubectl apply -f https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/cloudwatch-namespace.yaml
```
2. Run the following command to create a ConfigMap named cluster-info with the cluster name and the Region to send logs to. Replace cluster-name and cluster-region with your cluster's name and Region, in this case cluster-name=eks-workshop-cluster and region=us-east-2
```bashk
ClusterName=cluster-name
RegionName=cluster-region
FluentBitHttpPort='2020'
FluentBitReadFromHead='Off'
[[ ${FluentBitReadFromHead} = 'On' ]] && FluentBitReadFromTail='Off'|| FluentBitReadFromTail='On'
[[ -z ${FluentBitHttpPort} ]] && FluentBitHttpServer='Off' || FluentBitHttpServer='On'
kubectl create configmap fluent-bit-cluster-info \
--from-literal=cluster.name=${ClusterName} \
--from-literal=http.server=${FluentBitHttpServer} \
--from-literal=http.port=${FluentBitHttpPort} \
--from-literal=read.head=${FluentBitReadFromHead} \
--from-literal=read.tail=${FluentBitReadFromTail} \
--from-literal=logs.region=${RegionName} -n amazon-cloudwatch
```
Validate the config parameter using the command:
```bash
kubectl describe configmap fluent-bit-cluster-info -n amazon-cloudwatch
```

3. Download and deploy the Fluent Bit daemonset to the cluster by running one of the following commands.
```bash
kubectl apply -f https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/fluent-bit/fluent-bit.yaml
```

4. Validate the deployment by entering the following command. Each node should have one pod named fluent-bit-*.
```bash
kubectl get pods -n amazon-cloudwatch
```