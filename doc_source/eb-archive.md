# Amazon EventBridge archive and replay<a name="eb-archive"></a>

In EventBridge, you can create an archive of [events](eb-events.md) so that you can easily replay them at a later time\. For example, you might want to replay events to recover from errors or to validate new functionality in your application\.

**Note**  
There may be a delay between an event being published to an event bus and the event arriving in the archive\. We recommend you delay replaying archived events for 10 minutes to make sure all events are replayed\.



**Topics**
+ [Archiving Amazon EventBridge events](eb-archive-event.md)
+ [Replaying archived Amazon EventBridge events](eb-replay-archived-event.md)