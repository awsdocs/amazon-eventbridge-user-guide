# Archiving Amazon EventBridge events<a name="eb-archive-event"></a>

When you create an archive in EventBridge, you can determine which [events](eb-events.md) are sent to the archive by specifying an [event pattern](eb-event-patterns.md)\. EventBridge sends events that match the event pattern to the archive\. You also set the retention period to store events in the archive before they are discarded\.

By default, EventBridge encrypts event data in an archive using 256\-bit Advanced Encryption Standard \(AES\-256\) under an [AWS owned CMK](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-owned-cmk), which helps secure your data from unauthorized access\.

**To create an archive for all events**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the left navigation pane, choose **Archives**\.

1. Choose **Create archive**\.

1. Under **Archive detail**, enter a **Name** for the archive\. The name must be unique to your account in the selected Region\.

   You can't change the name after you create the archive\.

1. \(Optional\) Enter a **Description** for the archive\.

1. For **Source**, select the event bus that emits the events to send to the archive\.

1. For **Retention period**, do one of the following:
   + Choose **Indefinite** to retain the events in the archive and not ever delete them\.
   + Enter the number of days to retain the events\. After the number of days specified, EventBridge deletes the events from the archive\.

1. Choose **Next**\.

1. Under **Event pattern**, choose **No event filtering**\. 

1. Choose **Create archive**\.

**To create an archive with an event pattern**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the left navigation pane, choose **Archives**\.

1. Choose **Create archive**\.

1. Under **Archive detail**, enter a **Name** for the archive\. The name must be unique to your account in the selected Region\.

   You can't change the name after you create the archive\.

1. \(Optional\) Enter a **Description** for the archive\.

1. For **Source**, select the event bus that emits the events to send to the archive\. 

1. For Retention period, do one of the following:
   + Choose **Indefinite**\* to retain the events in the archive and not ever delete them\.
   + Enter the number of days to retain the events\. After the number of days specified, EventBridge deletes the events from the archive\.

1. Choose **Next**\.

1. Under **Event pattern**, choose **Filtering events by event pattern matching**\.

1. Do one of the following:
   + Select **Pattern builder**, then choose the **Service provider**\. If you choose **AWS**, also select the **AWS service name** and **Event type** to use in the pattern\.
   + Select **JSON editor** to create a pattern manually\. You can also copy the pattern from a rule and then paste it into the JSON editor\.

1. Choose **Create archive**\.

To confirm that events are successfully sent to the archive, you can use the [https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_DescribeArchive.html](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_DescribeArchive.html) operation of the EventBridge API to retrieve details for the archive\. The value returned for `EventCount` reflects the number of events in the archive\. If it is 0, there are no events in the archive\.