## Week 0 — Billing and Architecture

## Project Scenario
Building "Cruddur" an Ephemeral-first Micro Blogging Application (like Twitter) using AWS services and external services like Momento.

## Technical Tasks
In the week 00, we are going to lay out the foundation for the entire boot camp by:
- Discussing the format of the boot camp
- Going over the business use case of our Cruddur project
- Looking at an architectural diagram of what we plan to build
- Showcase how to use Lucid Charts to build architectures
- Talk about C4 Models
- Running through the cloud services we will utilize
- Testing that we can access our AWS accounts
- Settings up AWS free-tier and understand how to track spend in AWS
  eg . AWS Budgets, AWS Cost Explorer, Billing Alarms
- Understanding how to look at monthly billing reports
- Launching AWS CloudShell and looking at AWS CLI
- Generating AWS credentials

## Various Model Types
- Good architecture addresses concerns of Risks, Assumptions, and constraints (RRACs)
- C4 Model:
- A set of hierarchical abstractions (software systems, containers, components, and code). A set of hierarchical diagrams (system context, containers, components, and code). Notation and tooling independent.
 ![c4-overview](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/689711f0-7177-46d0-9a1a-a4f3d5fcc47c)

Reference: https://c4model.com/

- TOGAF Architecture:
- The Open Group Architecture Framework (TOGAF) is an enterprise architecture methodology that offers a high-level framework for enterprise software development. TOGAF helps organize the development process through a systematic approach aimed at reducing errors, maintaining timelines, staying on budget, and aligning IT with business units to produce quality results.
![togaf](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/a5930714-7238-4229-89d9-ae3110896beb)

Reference: https://www.opengroup.org/togaf

- AWS's Well architected framework
- The AWS Well-Architected Framework helps you understand the pros and cons of decisions you make while building systems on AWS. By using the Framework you will learn architectural best practices for designing and operating reliable, secure, efficient, cost-effective, and sustainable systems in the cloud.
- Pillars
1. Operational excellence
2. Security
3. Reliability
4. Performance efficiency
5. Cost optimization
6. Sustainability
   
Reference: https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html
## Types of Designs
- Conceptual (Napkin): Basic level design by stakeholders and architects. This design defines & organizes rules/concepts.
- Logical: Shows system implementation, and designed infrastructure without exact labels like names, sizes, and IP addresses.
- Physical: Actual architectural design with granular details like names, environments, instances, IP ranges, etc.
  
## My Conceptual (Napkin) Design
![Cruddur napkin design Dhana](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/722259a8-cead-48d6-ba1b-5992b5e9d210)


## My Logical Design using Lucid Charts
![Cruddur-logical Diagram -DhanashriD](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/908570d8-c7a2-4051-9d9d-ffd496db52da)

### Install AWS CLI

- Install the AWS CLI when our Gitpod enviroment.
- We are are going to set AWS CLI to use partial autoprompt mode to make it easier to debug CLI commands.
- The bash commands we are using are the same as the [AWS CLI Install Instructions]https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html


### Create a new User and Generate AWS Credentials

- Go to (IAM Users Console](https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#/users) andrew create a new user
- `Enable console access` for the user
- Create a new `Admin` Group and apply `AdministratorAccess`
- Create the user and go find and click into the user
- Click on `Security Credentials` and `Create Access Key`
- Choose AWS CLI Access
- Download the CSV with the credentials

### Set Env Vars

We will set these credentials for the current bash terminal
```
export AWS_ACCESS_KEY_ID=""
export AWS_SECRET_ACCESS_KEY=""
export AWS_DEFAULT_REGION=us-east-1
```

We'll tell Gitpod to remember these credentials if we relaunch our workspaces
```
gp env AWS_ACCESS_KEY_ID=""
gp env AWS_SECRET_ACCESS_KEY=""
gp env AWS_DEFAULT_REGION=us-east-1
```

### Check that the AWS CLI is working and you are the expected user

```sh
aws sts get-caller-identity
```

You should see something like this:
```json
{
    "UserId": "AIFBZRJIQN2ONP4ET4EK4",
    "Account": "655602346534",
    "Arn": "arn:aws:iam::655602346534:user/andrewcloudcamp"
}
```

now,

Update our `.gitpod.yml` to include the following task.

```sh
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
```

We'll also run these commands indivually to perform the install manually

## Enable Billing 

We need to turn on Billing Alerts to recieve alerts...


- In your Root Account go to the [Billing Page](https://console.aws.amazon.com/billing/)
- Under `Billing Preferences` Choose `Receive Billing Alerts`
- Save Preferences


## Creating a Billing Alarm

### Create SNS Topic

- We need an SNS topic before we create an alarm.
- The SNS topic is what will delivery us an alert when we get overbilled
- [aws sns create-topic](https://docs.aws.amazon.com/cli/latest/reference/sns/create-topic.html)

We'll create a SNS Topic
```sh
aws sns create-topic --name billing-alarm
```
which will return a TopicARN

We'll create a subscription supply the TopicARN and our Email
```sh
aws sns subscribe \
    --topic-arn TopicARN \
    --protocol email \
    --notification-endpoint your@email.com
```

Check your email and confirm the subscription

#### Create Alarm

- [aws cloudwatch put-metric-alarm](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html)
- [Create an Alarm via AWS CLI](https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-estimatedcharges-alarm/)
- We need to update the configuration json script with the TopicARN we generated earlier
- We are just a json file because --metrics is is required for expressions and so its easier to us a JSON file.

```sh
aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm_config.json
```

## Create an AWS Budget

[aws budgets create-budget](https://docs.aws.amazon.com/cli/latest/reference/budgets/create-budget.html)

Get your AWS Account ID
```sh
aws sts get-caller-identity --query Account --output text
```

- Supply your AWS Account ID
- Update the json files
- This is another case with AWS CLI its just much easier to json files due to lots of nested json

```sh
aws budgets create-budget \
    --account-id AccountID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json
```
