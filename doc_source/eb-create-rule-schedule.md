# Creating an Amazon EventBridge rule that runs on a schedule<a name="eb-create-rule-schedule"></a>

A [rule](eb-rules.md) can run in response to an [event](eb-events.md) or at certain time intervals\. For example, to periodically run an AWS Lambda function, you can create a rule to run on a schedule\. You can create rules that run on a schedule by using cron or rate expressions\. All scheduled events use UTC\+0 time zone, and the minimum precision for a schedule is one minute\. Your scheduled rule runs within that minute, but not on the precise 0th second\.

EventBridge supports cron expressions and rate expressions\. Rate expressions are simpler to define and cron expressions offer the detailed schedule control\. For example, with a cron expression, you can define a rule that runs at a specified time on a certain day of each week or month\. In contrast, rate expressions run a rule at a regular rate, such as once every hour or once every day\.

**Note**  
EventBridge doesn't provide second\-level precision in schedule expressions\. The finest resolution using a cron expression is one minute\. Due to the distributed nature of EventBridge and the target services, there can be a delay of several seconds between the time the scheduled rule is triggered and the time the target service runs the target resource\. 



**Topics**
+ [Cron Expressions](#eb-cron-expressions)
+ [Rate Expressions](#eb-rate-expressions)
+ [Create rule](#eb-create-scheduled-rule)

## Cron Expressions<a name="eb-cron-expressions"></a>

Cron expressions have six required fields, which are separated by white space\. 

**Syntax**

```
cron(fields)
```


| **Field** | **Values** | **Wildcards** | 
| --- | --- | --- | 
|  Minutes  |  0\-59  |  , \- \* /  | 
|  Hours  |  0\-23  |  , \- \* /  | 
|  Day\-of\-month  |  1\-31  |  , \- \* ? / L W  | 
|  Month  |  1\-12 or JAN\-DEC  |  , \- \* /  | 
|  Day\-of\-week  |  1\-7 or SUN\-SAT  |  , \- \* ? L \#  | 
|  Year  |  1970\-2199  |  , \- \* /  | 

**Wildcards**
+ The **,** \(comma\) wildcard includes additional values\. In the Month field, JAN,FEB,MAR includes January, February, and March\.
+ The **\-** \(dash\) wildcard specifies ranges\. In the Day field, 1\-15 includes days 1 through 15 of the specified month\.
+ The **\*** \(asterisk\) wildcard includes all values in the field\. In the Hours field, **\*** includes every hour\. You can't use **\*** in both the Day\-of\-month and Day\-of\-week fields\. If you use it in one, you must use **?** in the other\.
+ The **/** \(slash\) wildcard specifies increments\. In the Minutes field, you could enter 1/10 to specify every tenth minute, starting from the first minute of the hour \(for example, the 11th, 21st, and 31st minute, and so on\)\.
+ The **?** \(question mark\) wildcard specifies any\. In the Day\-of\-month field you could enter **7** and if any day of the week was acceptable, you could enter **?** in the Day\-of\-week field\.
+ The **L** wildcard in the Day\-of\-month or Day\-of\-week fields specifies the last day of the month or week\.
+ The **W** wildcard in the Day\-of\-month field specifies a weekday\. In the Day\-of\-month field, **3W** specifies the weekday closest to the third day of the month\.
+ The **\#** wildcard in the Day\-of\-week field specifies a certain instance of the specified day of the week within a month\. For example, 3\#2 would be the second Tuesday of the month: the 3 refers to Tuesday because it is the third day of each week, and the 2 refers to the second day of that type within the month\.
**Note**  
If you use a '\#' character, you can define only one expression in the day\-of\-week field\. For example, `"3#1,6#3"` is not valid because it is interpreted as two expressions\.

**Limitations**
+ You can't specify the Day\-of\-month and Day\-of\-week fields in the same cron expression\. If you specify a value or a \* \(asterisk\) in one of the fields, you must use a **?** \(question mark\) in the other\.
+ Cron expressions that lead to rates faster than 1 minute are not supported\.

**Examples**  
You can use the following sample cron strings when creating a rule with schedule\.


| Minutes | Hours | Day of month | Month | Day of week | Year | Meaning | 
| --- | --- | --- | --- | --- | --- | --- | 
|  0  |  10  |  \*  |  \*  |  ?  |  \*  |  Run at 10:00 am \(UTC\+0\) every day  | 
|  15  |  12  |  \*  |  \*  |  ?  |  \*  |  Run at 12:15 pm \(UTC\+0\) every day  | 
|  0  |  18  |  ?  |  \*  |  MON\-FRI  |  \*  |  Run at 6:00 pm \(UTC\+0\) every Monday through Friday  | 
|  0  |  8  |  1  |  \*  |  ?  |  \*  |  Run at 8:00 am \(UTC\+0\) every 1st day of the month  | 
|  0/15  |  \*  |  \*  |  \*  |  ?  |  \*  |  Run every 15 minutes  | 
|  0/10  |  \*  |  ?  |  \*  |  MON\-FRI  |  \*  |  Run every 10 minutes Monday through Friday  | 
|  0/5  |  8\-17  |  ?  |  \*  |  MON\-FRI  |  \*  |  Run every 5 minutes Monday through Friday between 8:00 am and 5:55 pm \(UTC\+0\)  | 
|  0/30  |  20\-2  |  ?  |  \*  |  MON\-FRI  |  \*  |  Run every 30 minutes Monday through Friday between 10:00 pm on the starting day to 2:00 am on the following day \(UTC\) Run from 12:00 am to 2:00 am on Monday morning \(UTC\)\.  | 



The following example creates a rule that runs every day at 12:00pm UTC\+0\.

```
aws events put-rule --schedule-expression "cron(0 12 * * ? *)" --name MyRule1
```

The following example creates a rule that runs every day, at 2:05pm and 2:35pm UTC\+0\.

```
aws events put-rule --schedule-expression "cron(5,35 14 * * ? *)" --name MyRule2
```

The following example creates a rule that runs at 10:15am UTC\+0 on the last Friday of each month during the years 2019 to 2022\.

```
aws events put-rule --schedule-expression "cron(15 10 ? * 6L 2019-2022)" --name MyRule3
```

## Rate Expressions<a name="eb-rate-expressions"></a>

A *rate expression* starts when you create the scheduled event rule, and then it runs on a defined schedule\.

Rate expressions have two required fields separated by white space\.

**Syntax**

```
rate(value unit)
```

value  
A positive number\.

unit  
The unit of time\. Different units are required for values of 1, such as `minute`, and values over 1, such as `minutes`\.  
Valid values: minute \| minutes \| hour \| hours \| day \| days

**Limitations**  
If the value is equal to 1, then the unit must be singular\. If the value is greater than 1, the unit must be plural\. For example, rate\(1 hours\) and rate\(5 hour\) aren't valid, but rate\(1 hour\) and rate\(5 hours\) are valid\.

**Examples**  
The following examples show how to use rate expressions with the AWS CLI  `put-rule` command\. The first example triggers the rule minute, the next triggers it every five minutes, the third example triggers it once an hour, and the final example triggers it once per day\.

```
aws events put-rule --schedule-expression "rate(1 minute)" --name MyRule2
```

```
aws events put-rule --schedule-expression "rate(5 minutes)" --name MyRule3
```

```
aws events put-rule --schedule-expression "rate(1 hour)" --name MyRule4
```

```
aws events put-rule --schedule-expression "rate(1 day)" --name MyRule5
```

## Create rule<a name="eb-create-scheduled-rule"></a>

The following steps walk you through how to create an EventBridge rule that triggers on a regular schedule\.

**Note**  
You can only create scheduled rules using the default event bus\.

**To create a rule that runs on a regular schedule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Event bus**, choose the event bus that you want to associate with this rule\. If you want this rule to match events that come from your account, select ** AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\.

1. For **Rule type**, choose **Schedule**\.

1. Choose **Next**\.

1. For **Schedule pattern**, do one the following:
   + To use a cron expression to define the schedule, choose **A fine\-grained schedule that runs at a specific time, such as 8:00 a\.m\. PST on the first Monday of every month\.** and enter the cron expression\.
   + To use a rate expression to define the schedule, choose **A schedule that runs at a regular rate, such as every 10 minutes\.** and enter the rate expression\.

1. Choose **Next**\.

1. For **Target types**, choose **AWS service**\.

1. For **Select a target**, choose the AWS service that you want to send information to when EventBridge detects an event that matches the event pattern\.

1. The fields displayed vary depending on the service you choose\. Enter information specific to this target type as needed\. 

1. For many target types, EventBridge needs permissions to send events to the target\. In these cases, EventBridge can create the IAM role needed for your rule to run\. Do one of the following:
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
   + To use an IAM role that you created earlier, choose **Use existing role** and select the existing role from the drop\-down list\.

1. \(Optional\) For **Additional settings**, do the following:

   1. For **Maximum age of event**, enter a value between one minute \(00:01\) and 24 hours \(24:00\)\.

   1. For **Retry attempts**, enter a number between 0 and 185\.

   1. For **Dead\-letter queue**, choose whether to use a standard Amazon SQS queue as a dead\-letter queue\. EventBridge sends events that match this rule to the dead\-letter queue if they are not successfully delivered to the target\. Do one of the following:
      + Choose **None** to not use a dead\-letter queue\.
      + Choose **Select an Amazon SQS queue in the current AWS account to use as the dead\-letter queue** and then select the queue to use from the drop\-down list\.
      + Choose **Select an Amazon SQS queue in an other AWS account as a dead\-letter queue** and then enter the ARN of the queue to use\. You must attach a resource\-based policy to the queue that grants EventBridge permission to send messages to it\. For more information, see [Granting permissions to the dead\-letter queue](eb-rule-dlq.md#eb-dlq-perms)\.

1. \(Optional\) Choose **Add another target** to add another target for this rule\.

1. Choose **Next**\.

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Amazon EventBridge tags](eb-tagging.md)\.

1. Choose **Next**\.

1. Review the details of the rule and choose **Create rule**\.