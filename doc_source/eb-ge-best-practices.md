# Best practices for working with Amazon EventBridge global endpoints<a name="eb-ge-best-practices"></a>

The following best practices are recommended when you set up global endpoints\.

**Topics**
+ [Enabling event replication](#eb-ge-bp-enable-replication)
+ [Preventing event throttling](#eb-ge-bp-throttling)
+ [Using subscriber metrics in Amazon Route 53 health checks](#eb-ge-bp-sub-metrics)

## Enabling event replication<a name="eb-ge-bp-enable-replication"></a>

We strongly recommend that you turn on replication and process your events in the secondary Region that you assign to your global endpoint\. This ensures that your application in the secondary Region is configured correctly\. You should also turn on replication to ensure automatic recovery to the primary Region after an issue has been mitigated\.

Event IDs can change across API calls so correlating events across Regions requires you to have an immutable, unique identifier\. Consumers should also be designed with idempotency in mind\. That way, if you're replicating events, or replaying them from archives, there are no side effects from the events being processed in both Regions\.

## Preventing event throttling<a name="eb-ge-bp-throttling"></a>

To prevent events from being throttled, we recommend updating your `PutEvents` and targets limits so they're consistent across Regions\.

## Using subscriber metrics in Amazon Route 53 health checks<a name="eb-ge-bp-sub-metrics"></a>

Avoid including subscriber metrics in your Amazon Route 53 health checks\. Including these metrics may cause your publisher to failover to the secondary Regions if a subscriber encounters an issue despite all other subscribers remaining healthy in the primary Region\. If one of your subcribers is failing to process events in the primary Region, you should turn on replication to ensure that your subscriber in the secondary Region can process events successfully\.