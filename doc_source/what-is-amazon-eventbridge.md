# What Is Amazon EventBridge?<a name="what-is-amazon-eventbridge"></a>

Amazon EventBridge is a serverless event bus service that makes it easy to connect your applications with data from a variety of sources\. EventBridge delivers a stream of real\-time data from your own applications, Software\-as\-a\-Service \(SaaS\) applications, and AWS services and routes that data to targets such as AWS Lambda\. You can set up routing rules to determine where to send your data to build application architectures that react in real time to all of your data sources\. EventBridge allows you to build event driven architectures, which are loosely coupled and distributed\. 

EventBridge was formerly called Amazon CloudWatch Events\. It includes new features that enable you to receive events from SaaS partners and your own applications\. Existing CloudWatch Events users can access their existing default bus, rules, and events in the new EventBridge console and in the CloudWatch Events console\. EventBridge uses the same CloudWatch Events API, so all of your existing CloudWatch Events API usage remains the same\.

You can configure the following AWS resources as targets for EventBridge:
+ Lambda functions
+ Amazon EC2 instances
+ Streams in Amazon Kinesis Data Streams
+ Delivery streams in Amazon Kinesis Data Firehose
+ Log groups in Amazon CloudWatch Logs
+ Amazon ECS tasks
+ Systems Manager Run Command
+ Systems Manager Automation
+ AWS Batch jobs
+ AWS Step Functions state machines
+ Pipelines in AWS CodePipeline
+ AWS CodeBuild projects
+ Amazon Inspector assessment templates
+ Amazon SNS topics
+ Amazon SQS queues
+ Built\-in targets: `EC2 CreateSnapshot API call`, `EC2 RebootInstances API call`, `EC2 StopInstances API call`, and `EC2 TerminateInstances API call`
+ The default event bus of another AWS account

## Concepts<a name="event-bridge-components"></a>

Before you begin using EventBridge, you should understand the following concepts:
+ **Events** – An event indicates a change in an environment\. This can be an AWS environment, an SaaS partner service or application, or one of your own custom applications or services\. For example, Amazon EC2 generates an event when the state of an EC2 instance changes from pending to running, and Amazon EC2 Auto Scaling generates events when it launches or terminates instances\. AWS CloudTrail publishes events when you make API calls\. You can also set up scheduled events that are generated on a periodic basis\. For a list of services that generate events, and sample events from each service, see [EventBridge Event Examples from Supported AWS Services](event-types.md)\.
+ **Rules** – A rule matches incoming events and routes them to targets for processing\. A single rule can route to multiple targets, all of which are processed in parallel\. Rules aren't processed in a particular order\. This enables different parts of an organization to look for and process the events that are of interest to them\. A rule can customize the JSON sent to the target, by passing only certain parts or by overwriting it with a constant\.
+ **Targets** – A target processes events\. Targets can include Amazon EC2 instances, Lambda functions, Kinesis streams, Amazon ECS tasks, Step Functions state machines, Amazon SNS topics, Amazon SQS queues, and built\-in targets\. A target receives events in JSON format\.

  A rule's targets must be in the same Region as the rule\.
+ **Event buses** – An event bus receives events\. When you create a rule, you associate it with a specific event bus, and the rule is matched only to events received by that event bus\.

  Your account has one default event bus, which receives events from AWS services\. You can create custom event buses to receive events from your custom applications\. You can also create partner event buses to receive events from SaaS partner applications\.
+ **Partner event sources** – A partner event source is used by an AWS partner to send events to an AWS customer account\. To receive these events, the customer must associate an event bus with the partner event source\.

## Related AWS Services<a name="eventbridge-related-services"></a>

You can use the following services with EventBridge:
+ **AWS CloudTrail** enables you to monitor the calls made to the EventBridge API for your account, including calls made by the AWS Management Console, the AWS CLI and other services\. When CloudTrail logging is turned on, EventBridge writes log files to an S3 bucket\. Each log file contains one or more records, depending on how many actions are performed to satisfy a request\. For more information, see [Logging and Monitoring in Amazon EventBridge](logging-cw-api-calls-eventbridge.md)\.
+ **AWS CloudFormation** enables you to model and set up your AWS resources\. You create a template that describes the AWS resources you want, and AWS CloudFormation takes care of provisioning and configuring those resources for you\. You can use EventBridge rules in your AWS CloudFormation templates\. For more information, see [AWS::Events::Rule](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-events-rule.html) in the *AWS CloudFormation User Guide*\.
+ **AWS Config** enables you to record configuration changes to your AWS resources\. This includes how resources relate to one another and how they were configured in the past, so that you can see how the configurations and relationships change over time\. You can also create AWS Config rules to check whether your resources are compliant or noncompliant with your organization's policies\. For more information, see the [AWS Config Developer Guide](https://docs.aws.amazon.com/config/latest/developerguide/)\.
+ **AWS Identity and Access Management \(IAM\)** helps you securely control access to AWS resources for your users\. Use IAM to control who can use your AWS resources \(authentication\), what resources they can use, and how they can use them \(authorization\)\. For more information, see [Identity and Access Management in Amazon EventBridge](auth-and-access-control-eventbridge.md)\.
+ **Amazon Kinesis Data Streams** enables rapid and nearly continuous data intake and aggregation\. The type of data used includes IT infrastructure log data, application logs, social media, market data feeds, and web clickstream data\. Because the response time for the data intake and processing is in real time, processing is typically lightweight\. For more information, see the [Amazon Kinesis Data Streams Developer Guide](https://docs.aws.amazon.com/streams/latest/dev/)\.
+ **AWS Lambda** enables you to build applications that respond quickly to new information\. Upload your application code as Lambda functions, and Lambda runs your code on high\-availability compute infrastructure\. Lambda performs all the administration of the compute resources, including server and operating system maintenance, capacity provisioning, automatic scaling, code and security patch deployment, and code monitoring and logging\. For more information, see the [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/)\.