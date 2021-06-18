# Replaying archived Amazon EventBridge events<a name="eb-replay-archived-event"></a>

After you create an archive, you can then replay [events](eb-events.md) from the archive\. For example, if you update an application with additional functionality, you can replay historical events to ensure that the events are reprocessed to keep the application consistent\. You can also use an archive to replay events for new functionality\. When you replay events, you can specify which archive to replay events from, the start and end time for the event to replay, the [event bus](eb-event-bus.md), or one or more [rules](eb-rules.md) to replay the events to\.

Events aren't necessarily replayed in the same order that they were added to the archive\. A replay processes events to replay based on the time in the event, and replays them on one minute intervals\. If you specify an event start time and an event end time that covers a 20 minute time range, the events are replayed from the first minute of that 20 minute range first\. Then the events from the second minute are replayed\. You can use the `DescribeReplay` operation of the EventBridge API to determine the progress of a replay\. `EventLastReplayedTime` returns the time stamp of the last event replayed\.

Events are replayed based on, but separate from, the `PutEvents` transactions per second limit for the AWS account\. You can request an increase to the limit for PutEvents\. For more information, see [ Amazon EventBridge Quotas](https://docs.aws.amazon.com/eventbridge/latest/userguide/cloudwatch-limits-eventbridge.html)\.

**Note**  
You can have a maximum of 10 active concurrent replays per account per AWS Region\.

**To start an event replay**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the left navigation pane, choose **Replays**\.

1. Choose **Start new replay**\.

1. Enter a **Name** for the replay and, optionally, a **Description**\.

1. For **Source**, select the archive to replay events from\.

1. For destination, you can replay events only to the same event bus that emitted the events\.

1. For **Specify rules**, do one of the following:
   + Choose **All rules** to replay events to all rules\.
   + Choose **Specify rules**, and then select the rule or rules to replay the events to\.

1. Under **Replay time frame**, specify the **Date**, **Time**, and **Time zone** for the **Start time** and the **End time**\. Only events that events that occurred between the **Start time** and **End time** are replayed\. 

1. Choose **Start replay**\.

When the events from the archived are replayed, the status of the replay is **Completed**\.

If you start a replay and then want to interrupt it, you can cancel it as long as the status ois**Starting** or **Running**\.

**To cancel a replay**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the left navigation pane, choose **Replays**\.

1. Choose the replay to cancel\.

1. Choose **Cancel**\.