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

## Setup and Perform a load test

We now have monitoring enabled for our cluster. Let us push the limits of our system to see how the metrics may change. To perform our load test, we will use the tool `Siege` (https://github.com/JoeDog/siege). To install Siege on Cloud9, execute the following command:

```sh
sudo yum -y install siege
```

1. Write [a script](../run_load_test.sh) to run the siege load test

2. Execute the script

```sh
bash run_load_test.sh
```

This command will execute the Siege tool and it will drive 200 concurrent connections to the ECS application.

You can leave the tool running for 15-20 seconds and then you can kill the process with `Ctrl + C` if it doesn't terminate by itself.

## Load testing metrics

Go back to the Container Insights metrics and select `Performance monitoring` | `ECS Service` | `container-observability-app-***` and the time range of `5 min`:

![insights-5mins](/images/insights-5mins.png)

Notice the CPU utilization increasing as Siege increases the load on the application.

## Accessing CloudWatch Logs Insights

Let us explore the CPU utilization increase from another angle, using CloudWatch Logs Insights:

1. Navigate to CloudWatch Logs Insights and select the `/aws/ecs/containerinsights/<cluster-name>/performance` log group

2. Copy and paste the following filter command:

```
stats avg(MemoryUtilized) as Avg_Memory, avg(CpuUtilized) as Avg_CPU by bin(5m) | filter Type="Task"
```

3. Select the `Visualization` tab, then the `Bar` chart. You will see a screen like the following:

![log-insights-metrics-stats-1](/images/log-insights-metrics-stats-1.png)
![log-insights-metrics-stats-2](/images/log-insights-metrics-stats-2.png)

With CloudWatch Container Insights, we removed the need to manage and update your monitoring infrastructure and used native AWS solutions for which you don’t have to manage the platform.