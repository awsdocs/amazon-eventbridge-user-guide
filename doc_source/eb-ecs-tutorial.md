# Tutorial: Run an Amazon ECS task when a file is uploaded to an Amazon S3 bucket<a name="eb-ecs-tutorial"></a>

You can use EventBridge to run Amazon ECS tasks when specific AWS [events](eb-events.md) occur\.

In this tutorial, you set up an EventBridge [rule](eb-rules.md) that runs an [Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html) task when a file is uploaded to a specific [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html) bucket by using the Amazon S3 PUT operation\.

**Note**  
This tutorial assumes that you have already created the task definition in Amazon ECS\.

**Topics**
+ [Step 1: Create a Rule](#eb-run-ecs-s3-create-rule)
+ [Step 2: Confirm success](#success)
+ [Step 3: Clean up your resources](#cleanup)

## Step 1: Create a Rule<a name="eb-run-ecs-s3-create-rule"></a>

Create a rule to run an Amazon ECS task when a file is uploaded to an S3 bucket using the PUT operation\.

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

1. For **Define pattern**, do the following:

   1. Choose **Event pattern**\.

   1. Choose **Pre\-defined pattern by service**\.

   1. For **Service provider**, choose **AWS**\.

   1. For **Service Name**, choose **Simple Storage Service \(S3\)**\.

   1. For **Event type**, choose **Object Level Operations**\.

   1. Choose **Specific operation\(s\)**, **Put Object**\.

   1. Choose **Specific bucket\(s\) by name** and enter the name of the bucket\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it goes to your account’s default event bus\. 

1. For **Targets**, do the following:

   1. Choose **ECS task**\.

   1. For **Cluster** and **Task Definition**, select the resources that you created\.

   1. For **Launch Type**, choose `FARGATE` or `EC2`\. `FARGATE` appears only in Regions where AWS Fargate is supported\.

   1. \(Optional\) Specify a value for **Task Group**\. If the **Launch Type** is `FARGATE`, optionally specify a **Platform Version**\. Specify only the numeric portion of the platform version, such as 1\.1\.0\.

   1. \(Optional\) Specify a task definition revision and task count\. If you don't specify a task definition revision, EventBridge uses the latest revision\.

   1. If your task definition uses the awsvpc network mode, you must specify subnets and security groups\. All subnets and security groups must be in the same VPC\.

      If you specify more than one security group or subnet, separate them with commas but not spaces\.

      For **Subnets**, specify the entire `subnet-id` value for each subnet, as in the following example\.

      ```
      subnet-123abcd,subnet-789abcd
      ```

   1. Choose whether to allow the public IP address to be auto\-assigned\.

   1. Do one of the following: 
      + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
      + To use an IAM role that you created before, choose **Use existing role**\. This must be a role that already has sufficient permissions to invoke the build\. EventBridge doesn't grant additional permissions to the role that you select\.

1. Choose **Create**\.

## Step 2: Confirm success<a name="success"></a>

If you see the rule in the list of rules, you've successfully created the rule\.

## Step 3: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the EventBridge rule\(s\)**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule\(s\) that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.