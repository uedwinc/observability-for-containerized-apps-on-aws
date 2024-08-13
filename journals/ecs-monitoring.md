# Implementing observability for a distributed application running on Amazon ECS

We will see four procedures to collect metrics and logs from ECS clusters:

1. Collect cluster and service-level metrics.
2. Collect instance-level metrics using the CloudWatch agent.
3. Collect instance-level metrics using ADOT.
4. Collect logs and send them to CloudWatch Logs using FireLens.

## Container Insights on Amazon ECS for the cluster- and service-level metrics

You can activate Container Insights on Amazon ECS for the cluster- and service-level metrics on the account and cluster levels.

When you activate Container Insights on an account level, every new Amazon ECS cluster created after that will have Container Insights data collection activated by default.

### Container Insights on the account level

You can activate Container Insights on the account level using the AWS Management Console UI or the CLI.

1. To activate it using the AWS Management Console, go to https://console.aws.amazon.com/ecs/. In the menu on the left of the page, select `Account Settings`, click on the `Update` button in the top-right corner, and then at the bottom of the page, click on the checkbox under `CloudWatch Container Insights`, and then click on `Save changes`.

![account-level-insights](/images/account-level-insights.png)

2. To activate Container Insights on the account level using the CLI, execute the following command:

```sh
aws ecs put-account-setting --name "containerInsights" --value "enabled"
```

### Container Insights per cluster

Now, you can activate Container Insights per cluster if you want to save some costs on non-critical/low-margin environments or non-production environments. For new ECS clusters, you can also do it using the AWS Console Management UI or the CLI.

1. To create a new ECS cluster using the AWS Management Console with Container Insights enabled, you can follow the usual procedure to create a new cluster at https://console.aws.amazon.com/ecs/, making sure you click on the `Enable Container Insights` checkbox under `Monitoring`.

![container-level-insights](/images/container-level-insights.png)

2. If you are creating a new cluster using the CLI, you can do it with Container Insights enabled using the following command:

```sh
aws ecs create-cluster --cluster-name myCICluster --settings "name=containerInsights,value=enabled"
```

3. Activating Container Insights on an existing cluster is also easy. Assuming you have a variable named `clustername` with the cluster name, you need to execute the following command:

```sh
aws ecs update-cluster-settings --cluster ${clustername} --settings name=containerInsights,value=enabled --region ${AWS_REGION}
```