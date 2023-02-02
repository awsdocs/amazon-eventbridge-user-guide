# Amazon EventBridge Scheduler<a name="scheduler"></a>

 In addition to [scheduled rules](eb-create-rule-schedule.md), EventBridge now offers a new scheduling capability, Amazon EventBridge Scheduler\. EventBridge Scheduler is a serverless scheduler that allows you to create, run, and manage tasks from one central, managed service\. EventBridge Scheduler is highly customizable, and offers improved scalability over EventBridge scheduled rules, with a wider set of target API operations and AWS services\. EventBridge Scheduler allows you to schedule millions of tasks that can invoke more than 270 AWS services and over 6,000 API operations\. Without the need to provision and manage infrastructure, or integrate with multiple services, EventBridge Scheduler provides you with the ability to deliver schedules at scale and reduce maintenance costs\. 

 EventBridge Scheduler delivers your tasks reliably, with built\-in mechanisms that adjust your schedules based on the availability of downstream targets\. With EventBridge Scheduler, you can create schedules using cron and rate expressions for recurring patterns, or configure one\-time invocations\. You can set up flexible time windows for delivery, define retry limits, and set the maximum retention time for failed triggers\. 

We recommend that you use EventBridge Scheduler to invoke targets on a schedule\. For more information see the following\.

**EventBridge Scheduler resources**
+ [EventBridge Scheduler User Guide](https://docs.aws.amazon.com/scheduler/latest/UserGuide/index.html)
+ [EventBridge Scheduler API Reference](https://docs.aws.amazon.com/scheduler/latest/APIReference/Welcome.html)