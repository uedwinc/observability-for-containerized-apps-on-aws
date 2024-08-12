# Implementing observability for a distributed application running on Amazon EKS

We will see how to set up Amazon EKS cluster to use native CloudWatch features and Container Insights.

Amazon EKS lets you host your pods using EC2 worker nodes or AWS Fargate. When using EC2 worker nodes, we can collect metrics directly from the kubelet agent. But, when using AWS serverless compute for Fargate containers, no pod has access to the kubelet, so we need a different approach.

## Container Insights metrics on your EKS EC2 or customer-managed Kubernetes clusters

Whenever you want a system or workload to call AWS APIs on your behalf, you need to give it the correct permissions, which is similar to publishing metrics and logs to CloudWatch. Let’s attach the required policy to your worker node’s role.

1. Write [a script](../attach_role_cw_agent.sh) to attach `CloudWatchAgentServerPolicy` to your worker node’s role.

2. Execute the script

```sh
bash attach_role_cw_agent.sh
```

> This method is the easiest way to set up the correct permissions for your worker nodes. It works regardless of using Amazon EKS or deploying Kubernetes on Amazon EC2. But it gives permissions to all pods running inside the worker nodes to write data to CloudWatch. It may be okay for some workloads, but it doesn’t follow the least privilege security principle. Another option is to bind IAM roles to service accounts, which allows you to give permissions to only the required pods. If you prefer this way, see the documentation at https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-prerequisites.html.

> We are ready to install the necessary software to collect metrics and logs from your cluster. The product team did a fantastic job creating Docker containers with the required agents and plugins to make them integrate seamlessly into the AWS ecosystem. We will install two `DaemonSet` resources, one for the CloudWatch agent and another for Fluent Bit. `Fluent Bit` (https://fluentbit.io/) is a fast, lightweight processor, logging forwarder, and a Cloud Native Computing Foundation (`CNCF`) graduate project. Instead of recreating the wheel, the product team used an existing standard to send the cluster logs to CloudWatch.

> Even though we have the necessary Docker containers in public repositories (see https://gallery.ecr.aws/cloudwatch-agent/cloudwatch-agent and https://gallery.ecr.aws/aws-observability/aws-for-fluent-bit), we do have the task of writing the Kubernetes manifests to deploy the DaemonSet resources and all the surrounding objects such as `Namespace`, `ServiceAccount`, `ClusterRole`, `ClusterRoleBinding`, and `ConfigMaps`. The product team raises the bar once again and provides a Quick Start setup with all the necessary resources (see https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-EKS-quickstart.html).

3. Write and execute [a script](../install_cw_agent_fluentbit_k8s.sh) to install all the necessary DaemonSet resources

![daemonset-resources](/images/daemonset-resources.png)

After installing the `DaemonSet` resources, you should see the cluster metrics and logs in the `Container Insights` console

![cluster-insights](/images/cluster-insights.png)

> Update: Upgrading to Container Insights with enhanced observability for Amazon EKS

> https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-EKS-quickstart.html
> https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-upgrade-enhanced.html
> https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-EKS-addon.html

