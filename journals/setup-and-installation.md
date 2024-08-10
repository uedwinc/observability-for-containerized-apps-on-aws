# Set up a Cloud9 development workspace

1. Please click on the following link to create the required set of resources in your AWS account based on the AWS CloudFormation template:

1. Create a [CloudFormation template](/cloud9.yaml) for a Cloud9 environment to have a standard integrated development environment (IDE) with the required set of tools

2. Create a CloudFormation stack and upload the template for the stack

3. Enter a stack name. You can keep the default values and click on the checkbox asking for extra capabilities. Click on `Create stack`, and in a few minutes, you can find the environment URL in the CloudFormation `Outputs` tab:

![cloud9](/images/cloud9.png)

4. The setup of this environment requires some extra time after the CloudFormation execution to install extra tools. So, after the CloudFormation execution, wait for around 30 minutes just to be sure. You can check the setup progress by accessing `AWS Systems Manager` | `Run command`

![ssm-inprogress](/images/ssm-inprogress.png)

5. If you click on the `Outputs` URL, you will find a newly configured environment

![c9-console](/images/c9-console.png)

