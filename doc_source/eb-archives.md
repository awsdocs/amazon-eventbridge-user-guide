# EventBridge archives and event replays<a name="eb-archives"></a>

In EventBridge, you can create an archive of events so that you can easily replay them at a later time by starting an event replay\. You might want to replay events to, for example, recover from errors or validate new functionality in your application\. When you create an archive, you can determine which events are sent to the archive by specifying a filter pattern to match events\. Any events that match the filter criteria you specify are sent to the archive\. You also set a retention period to store events in the archive before they are discarded\.

By default, EventBridge encrypts event data in an archive using 256\-bit Advanced Encryption Standard \(AES\-256\) under an [AWS owned CMK](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-owned-cmk), which helps secure your data from unauthorized access\.

**To create an archive for all events**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the left navigation pane, choose **Archives**\.

1. Choose **Create archive**\.

1. Under **Archive detail**, enter a **Name** for the archive\. The name must be unique to your account in the selected Region\.

   You can't change the name after you create the archive\.

1. Optionally, enter a **Description** for the archive\.

1. For **Source**, select the event bus that emits the events to send to the archive\.

1. For **Retention period**, do one of the following:
   + Choose **Indefinite** to retain the events in the archive and not ever delete them\.
   + Enter the number of days to retain the events\. After the number of days specified events are deleted from the archive\.

1. Choose **Next**\.

1. Under **Event pattern**, choose **No event filtering**\. 

1. Choose **Create archive**\.

**To create an archive with an event filter pattern**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the left navigation pane, choose **Archives**\.

1. Choose **Create archive**\.

1. Under **Archive detail**, enter a **Name** for the archive\. The name must be unique to your account in the selected Region\.

   You can't change the name after you create the archive\.

1. Optionally, enter a **Description** for the archive\.

1. For **Source**, select the event bus that emits the events to send to the archive\. 

1. For Retention period, do one of the following:
   + Choose **Indefinite**\* to retain the events in the archive and not ever delete them\.
   + Enter the number of days to retain the events\. After the number of days specified events are deleted from the archive\.

1. Choose **Next**\.

1. Under **Event pattern**, choose **Filtering events by event pattern matching**\.

1. Do one of the following:
   + Select **Pattern builder**, then choose the **Service provider**\. If you choose **AWS**, also select the **AWS service name** and **Event type** to use in the pattern\.
   + Select **JSON editor** to create a pattern manually\. You can also copy the pattern from a rule and then paste it into the JSON editor\.

1. Choose **Create archive**\.

To confirm that events are successfully sent to the archive, you can use the [https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_DescribeArchive.html](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_DescribeArchive.html) operation of the EventBridge API to retrieve details for the archive\. The value returned for `EventCount` reflects the number of events in the archive\. If it is 0, no events have been sent to the archive\.

## Event replays<a name="eb-event-replay"></a>

After you create an archive, you can then replay events from the archive at a later time\. For example, if you update an application with additional functionality, you can replay historical events to ensure that the events are reprocessed to keep the application consistent\. You can also use an archive to replay events for new functionality\. When you create a replay, you can specify the archive to replay events from, the start and end time for the event to replay, and the target event bus or one or more rules to replay the events to\.

Events are not necessarily replayed in the exact same order that they were added to the archive\. A replay processes events to replay based on the time in the event, and replays them using 1 minute intervals\. If you specify an event start time> and an event end time that covers a 20 minute time range, the events are replayed from the first minute of that 20 minute range first\. Then the events from the second minute are replayed\. You can use the `DescribeReplay` operation of the EventBridge API to determine the progress of a replay\. The value returned for `EventLastReplayedTime` indicates the time within the specified time range associated with the last event replayed\.

Events are replayed based on, but separate from, the PutEvents transactions per second \(TPS\) limit for the account\. You can request an increase to the limit for PutEvents\. To learn more, see [ Amazon EventBridge Quotas](https://docs.aws.amazon.com/eventbridge/latest/userguide/cloudwatch-limits-eventbridge.html)\.

**Note**  
You can have a maximum of 10 active concurrent replays per account per Region\.

**To start an event replay**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the left navigation pane, choose **Replays**\.

1. Choose **Start new replay**\.

1. Enter a **Name** for the replay and, optionally, a **Description**\.

1. For **Source**, select the archive to replay events from\.

1. For destination, you can currently replay events only to the same event bus that emitted the events\.

1. For Specify rules, do one of the following:
   + Choose **All rules** to replay events to all rules\.
   + Choose **Specify rules**, and then select the rule or rules to replay the events to\.

1. Under **Replay time frame**, specify the **Date**, **Time**, and **Time zone** for the **Start time** and the **End time**\. Only events that events that occurred between the **Start time** and **End time** are replayed\. 

1. Choose **Start replay**\.

When the events from the archived are replayed, the status of the replay is **Completed**\.

If you start a replay and then want to interrupt it, you can cancel any replay with a status of **Starting** or **Running**\.

**To cancel a replay**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the left navigation pane, choose **Replays**\.

1. Select the radio button next to the replay to cancel\.

1. Choose **Cancel**\.