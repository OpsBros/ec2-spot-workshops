---
title: "Setup AWS CLI and clone the workshop repo"
weight: 40
---

{{% notice tip %}}
For this workshop, please ignore warnings about the version of pip being used.
{{% /notice %}}

1. Run the following command to view the current version of aws-cli:
```
aws --version
```

1. Update to the latest version:
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
. ~/.bash_profile
```

1. Confirm you have a newer version:
```
aws --version
```

Install dependencies for use in the workshop by running:

```
sudo yum -y install jq gettext
```

### Clone the GitHub repo

In order to execute the steps in the workshop, you'll need to clone the workshop GitHub repo.

In the Cloud9 IDE terminal, run the following command:

```
git clone https://github.com/awslabs/ec2-spot-workshops.git
```
Change into the workshop directory:

```
cd ec2-spot-workshops/workshops/ecs-spot-capacity-providers
```

Feel free to browse around. You can also browse the directory structure in the **Environment** tab on the left and even edit files directly there by double clicking on them.

We should configure our aws cli with our current region as default:

```
export ACCOUNT_ID=$(aws sts get-caller-identity  --output text --query Account)
export AWS_REGION=$(curl -s  169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
echo "export  ACCOUNT_ID=${ACCOUNT_ID}" >> ~/.bash_profile
echo "export  AWS_REGION=${AWS_REGION}" >> ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region

```

Use the commands below to set the CloudFormation stack name to an environment variable. 

{{% notice note %}}
**Select the tab** and follow the specific instructions depending on whether..
{{% /notice %}}


{{< tabs name="Region" >}}
    {{< tab name="You created the Stack manually" include="on_your_own_stack_name.md" />}}
    {{< tab name="Stack already created upfront at an AWS Event" include="at_an_aws_stack_name.md" />}}
{{< /tabs >}}


Run the command below to load CloudFormation outputs as the environment variables.

```
for output in $(aws cloudformation describe-stacks --stack-name ${STACK_NAME} --query 'Stacks[].Outputs[].OutputKey' --output text)
do
    export $output=$(aws cloudformation describe-stacks --stack-name ${STACK_NAME} --query 'Stacks[].Outputs[?OutputKey==`'$output'`].OutputValue' --output text)
    eval "echo $output : \"\$$output\""
done
```

***Congratulations***, your Cloud9 workspace setup is complete, and you can continue with this workshop.

