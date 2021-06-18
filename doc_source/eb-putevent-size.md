# Calculating Amazon EventBridge PutEvents event entry size<a name="eb-putevent-size"></a>

You can send custom [events](eb-events.md) to EventBridge by using the `PutEvents` action\. You can batch multiple event entries into one request for efficiency\. The total entry size must be less than 256KB\. You can calculate the entry size before you send the events\.



**Note**  
The size limit is imposed on the *entry*\. Even if the entry is less than the size limit, the *event* in EventBridge is always larger than the entry size due to the necessary characters and keys of the JSON representation of the event\. For more information, see [Amazon EventBridge events](eb-events.md)\.

EventBridge calculates the `PutEventsRequestEntry` size as follows:
+ If specified, the `Time` parameter is 14 bytes\.
+ The `Source` and `DetailType` parameters are the number of bytes for their UTF\-8 encoded forms\.
+ If specified, the `Detail` parameter is the number of bytes for its UTF\-8 encoded form\.
+ If specified, each entry of the `Resources` parameter is the number of bytes for its UTF\-8 encoded forms\.

The following example Java code calculates the size of a given `PutEventsRequestEntry` object\.

```
int getSize(PutEventsRequestEntry entry) {
    int size = 0;
    if (entry.getTime() != null) {
        size += 14;
    }
    size += entry.getSource().getBytes(StandardCharsets.UTF_8).length;
    size += entry.getDetailType().getBytes(StandardCharsets.UTF_8).length;
    if (entry.getDetail() != null) {
        size += entry.getDetail().getBytes(StandardCharsets.UTF_8).length;
    }
    if (entry.getResources() != null) {
        for (String resource : entry.getResources()) {
            if (resource != null) {
                size += resource.getBytes(StandardCharsets.UTF_8).length;
            }
        }
    }
    return size;
}
```

**Note**  
If the entry size is larger than 256KB, we recommend putting the event in an Amazon S3 object and including a link to that object in the `PutEvents` entry\.