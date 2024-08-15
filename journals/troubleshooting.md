# Understanding and troubleshooting performance bottlenecks in containers

## Build the environments

1. In the Cloud9 workspace, to ensure the service-linked roles exist for load balancers and ECS, run the following commands:

```sh
aws iam get-role --role-name "AWSServiceRoleForElasticLoadBalancing" || aws iam create-service-linked-role --aws-service-name "elasticloadbalancing.amazonaws.com"

aws iam get-role --role-name "AWSServiceRoleForECS" || aws iam create-service-linked-role --aws-service-name "ecs.amazonaws.com"
```

2. Write [a script](../deploy_demo_application.sh) that will set up a sample application in our AWS environment.

3. Now execute the script.

```sh
bash deploy_demo_application.sh
```

This script will take many minutes to execute, as it goes through the deployment process of all the different microservices. At some point, you will be required to give your environment a name eg dev, test, prod.

Now we have the infrastructure and sample application deployed. Next, let’s set up our container service to publish observability signals.

## Set up Container Insights

With the sample application deployed, let’s start to publish observability signals to Amazon CloudWatch Container Insights.

Execute the following command to enable Container Insights for our cluster:

```sh
aws ecs update-cluster-settings --cluster $(aws ecs list-clusters --query "clusterArns[*]" --output text | sed 's/\s\+/\n/g' | grep container-observability-app-) --settings name=containerInsights,value=enabled --region ${AWS_REGION}
```

## Explore Container Insights

After we have deployed the sample application and set the publication of observability signals using Container Insights, we are ready to monitor the application. Let’s check what we have available to do so.

1. Check that the logs are streaming into CloudWatch Logs. Navigate to CloudWatch Logs and search for a log group identified by `/aws/ecs/containerinsights/<cluster-name>/performance`.

![cluster-log-group](/images/cluster-log-group.png)

2. Now, navigate to the Amazon CloudWatch `Container Insights` console (https://console.aws.amazon.com/cloudwatch/home#container-insights:infrastructure). From the first drop-down box, select `Performance monitoring`, and in the two new drop-down boxes that will appear below, select `ECS Clusters` and `container-observability-app-***`, that is name of your cluster (from the second drop-down box):

![clusters-container-insights](/images/clusters-container-insights.png)

You should see a dashboard automatically created with the key metrics of your cluster, as in the preceding figure.

If you return to the resource list, from the first drop-down box, you can select the `container-observability-app-***` cluster and click on the `View logs` button. You will see the CloudWatch Logs Insights, where you can select a log group such as `/aws/ecs/containerinsights/container-observability-app-***/performance`, and run queries against more detailed data points. Check the following screenshot:

![performance logs-insights-1](/images/performance%20logs-insights-1.png)
![performance logs-insights-2](/images/performance%20logs-insights-2.png)

