# Set up a Cloud9 development workspace

1. Please click on the following link to create the required set of resources in your AWS account based on the AWS CloudFormation template:

1. Create a [CloudFormation template](/cloud9.yaml) for a Cloud9 environment to have a standard integrated development environment (IDE) with the required set of tools

![app-composer](/images/application-composer-2024-08-10T111846.614Z3te-cloud9.yaml.png)

2. Create a CloudFormation stack and upload the template for the stack

3. Enter a stack name. You can keep the default values and click on the checkbox asking for extra capabilities. Click on `Create stack`, and in a few minutes, you can find the environment URL in the CloudFormation `Outputs` tab:

![cloud9](/images/cloud9.png)

4. The setup of this environment requires some extra time after the CloudFormation execution to install extra tools. So, after the CloudFormation execution, wait for around 30 minutes just to be sure. You can check the setup progress by accessing `AWS Systems Manager` | `Run command`

![ssm-inprogress](/images/ssm-inprogress.png)

![ssm-complete](/images/ssm-complete.png)

5. If you click on the `Outputs` URL, you will find a newly configured environment

![c9-console](/images/c9-console.png)

# Set up an Amazon EKS cluster

We will now configure a sandbox environment

1. Create an [eksctl cluster file](../eks-ec2-eksctl.yaml)

2. Write [a bash script](../create-eks-ec2-eksctl.sh) to deploy the cluster

3. Execute the script as follows:

```sh
bash create-eks-ec2-eksctl.sh
```

The preceding command will start the creation of a new EKS cluster. You can see the cluster status in the command output. The process of creating a new cluster may take a few minutes.

4. After creating the cluster, let’s check the communication between the Cloud9 environment and the new cluster. Run the following command:

```sh
kubectl get svc
```

![get-svc](/images/get-svc.png)

This command retrieves all services deployed in the default namespace. It shows you can communicate with the Kubernetes API, and that you have the required permissions to execute commands.