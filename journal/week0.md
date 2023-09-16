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

## Security Considerations

The primary objective of cybersecurity within an organization is to identify and promptly notify the business about any technical risks and vulnerabilities that might expose the business to potential threats. Acquiring knowledge about AWS Organization Service Control Policies, as outlined in Ashish's repository, to strengthen and fortify the security of our organization (https://github.com/hashishrajan/aws-scp-best-practice-policies/). The Emphasis of cloud security is to mitigate the impact of breaches and safeguard networks and application services in cloud environments, thereby reducing the likelihood of data leaks due to human errors. Actively setting up an Organization within the root account, effectively establishing an Organizational Unit (OU) for improved organizational structure and management (hands-on implementation). Implementing AWS auditing services like CloudTrail to monitor all activities, encompassing data, security, and residency, thus enhancing overall security and compliance. I gained practical knowledge about the CloudShell service and actively managed the enabling and disabling of certain regions through the AWS console. Recognizing the critical importance of enabling Multi-Factor Authentication (MFA) for the root user, given its extensive access privileges within the AWS account, alongside IAM user configurations. Gaining insights into AWS GuardDuty and AWS Config, which are essential components of AWS security and compliance mechanisms.

### Install AWS CLI

- Install the AWS CLI when our Gitpod environment.
- Also set AWS CLI to use partial auto prompt mode to make it easier to debug CLI commands.
- The bash commands we are using are the same as the [AWS CLI Install Instructions]https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/16fc1453-3fb5-41bb-9720-049a80a6b876)

### Create a new User and Generate AWS Credentials

- Go to (IAM Users Console](https://us-east-1.console.aws.amazon.com/iamv2/home?region=ca-central-1#/users)
- create a new user, `Enable console access for the user
  ![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/c2f20339-a906-436b-940f-d1d5f29d21d3)
  
- Create a new `Admin` Group and apply `AdministratorAccess`
  ![grp](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/47d000ae-fa15-4985-8d44-ed81d0281d52)
  
- Create the user and go find and click into the user
- Click on `Security Credentials` and `Create Access Key` with AWS CLI Access
- Download the CSV with the credentials. It can be visible under the "Security Credentials" section
  ![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/2d652bb9-7e75-4ed8-bdc4-4dc196311c7c)

  
### Set Env Vars

Set these credentials for the current bash terminal
```
export AWS_ACCESS_KEY_ID=""
export AWS_SECRET_ACCESS_KEY=""
export AWS_DEFAULT_REGION=ca-central-1
```
Now, these set environmental variables can be viewed from our gitpod CLI prompt
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/89b201f8-ebea-4a34-8afb-aeda5da0eff4)


We'll tell Gitpod to remember these credentials if we relaunch our workspaces
```
gp env AWS_ACCESS_KEY_ID=""
gp env AWS_SECRET_ACCESS_KEY=""
gp env AWS_DEFAULT_REGION=ca-central-1
gp env ACCOUNT_ID=""
```

### Check that the AWS CLI is working and you are the expected user

```sh
aws sts get-caller-identity
```
Output:

![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/c89d0a79-33ae-4243-a1bf-c65a82029fce)

now,

Update our `.gitpod.yml` to include the following task. This task will run every time we set up a new gitpod workspace session and install AWS CLI.

```sh
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace/aws-bootcamp-cruddur-2023
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
```

The file will be updated like below:
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/de29f3ff-5c91-44fd-adcd-b577d508fb70)


## Enable Billing 

We need to turn on Billing Alerts to receive alerts...


- In your Root Account go to the [Billing Page](https://console.aws.amazon.com/billing/)
- Under `Billing Preferences` Choose `Receive Billing Alerts`
- Save Preferences

![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/1df8bd62-22b9-41d0-b8e5-077209ddd7bf)


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
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/7cf5fd98-cd87-43a1-80d1-184217533c50)

#### Create Alarm

- [aws cloudwatch put-metric-alarm](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html)
- [Create an Alarm via AWS CLI](https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-estimatedcharges-alarm/)
- We need to update the configuration json script with the TopicARN we generated earlier
- We are just a json file because --metrics is is required for expressions and so its easier to us a JSON file.

```sh
aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm_config.json
```
![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/5d8fd2e2-e133-43db-b5f7-f3074cf3381c)


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

![image](https://github.com/ddhaneumd/aws-bootcamp-cruddur-2023/assets/142753226/6ff3382b-421c-4b72-9ca6-7196171c5b5b)
