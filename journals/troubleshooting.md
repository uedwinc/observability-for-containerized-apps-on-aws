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

