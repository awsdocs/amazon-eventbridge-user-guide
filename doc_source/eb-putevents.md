# Adding Amazon EventBridge events with `PutEvents`<a name="eb-putevents"></a>

The `PutEvents` action sends multiple [events](eb-events.md) to EventBridge in a single request\. For more information, see [PutEvents](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_PutEvents.html) in the *Amazon EventBridge API Reference* and [put\-events](https://docs.aws.amazon.com/cli/latest/reference/events/put-events.html) in the *AWS CLI Command Reference*\.

Each `PutEvents` request can support a limited number of entries\. For more information, see [Amazon EventBridge quotas](eb-quota.md)\. The `PutEvents` operation attempts to process all entries in the natural order of the request\. After you call `PutEvents`, EventBridge assigns each event a unique ID\.

**Topics**
+ [Handling failures with `PutEvents`](#eb-failure-handling)
+ [Sending events using the AWS CLI](#eb-send-events-aws-cli)
+ [Calculating Amazon EventBridge PutEvents event entry size](eb-putevent-size.md)

The following example Java code sends two identical events to EventBridge\.

------
#### [ AWS SDK for Java Version 2\.x ]

```
EventBridgeClient eventBridgeClient =
    EventBridgeClient.builder().build();

PutEventsRequestEntry requestEntry = PutEventsRequestEntry.builder()
    .resources("resource1", "resource2")
    .source("com.mycompany.myapp")
    .detailType("myDetailType")
    .detail("{ \"key1\": \"value1\", \"key2\": \"value2\" }")
    .build();

List <
PutEventsRequestEntry > requestEntries = new ArrayList <
PutEventsRequestEntry > ();
requestEntries.add(requestEntry);

PutEventsRequest eventsRequest = PutEventsRequest.builder()
    .entries(requestEntries)
    .build();

PutEventsResponse result = eventBridgeClient.putEvents(eventsRequest);

for (PutEventsResultEntry resultEntry: result.entries()) {
    if (resultEntry.eventId() != null) {
        System.out.println("Event Id: " + resultEntry.eventId());
    } else {
        System.out.println("PutEvents failed with Error Code: " + resultEntry.errorCode());
    }
}
```

------
#### [ AWS SDK for Java Version 1\.0 ]

```
EventBridgeClient eventBridgeClient =
    EventBridgeClient.builder().build();
    
PutEventsRequestEntry requestEntry = new PutEventsRequestEntry()
        .withTime(new Date())
        .withSource("com.mycompany.myapp")
        .withDetailType("myDetailType")
        .withResources("resource1", "resource2")
        .withDetail("{ \"key1\": \"value1\", \"key2\": \"value2\" }");

PutEventsRequest request = new PutEventsRequest()
        .withEntries(requestEntry, requestEntry);

PutEventsResult result = awsEventsClient.putEvents(request);

for (PutEventsResultEntry resultEntry : result.getEntries()) {
    if (resultEntry.getEventId() != null) {
        System.out.println("Event Id: " + resultEntry.getEventId());
    } else {
        System.out.println("Injection failed with Error Code: " + resultEntry.getErrorCode());
    }
}
```

------

After you run this code, the `PutEvents` result includes an array of response entries\. Each entry in the response array corresponds to an entry in the request array in order from the beginning to the end of the request and response\. The response `Entries` array always includes the same number of entries as the request array\.

## Handling failures with `PutEvents`<a name="eb-failure-handling"></a>

By default, if an individual entry within a request fails, EventBridge continues processing the rest of the entries in the request\. A response `Entries` array can include both successful and unsuccessful entries\. You must detect unsuccessful entries and include them in a subsequent call\.

Successful result entries include an `Id` value, and unsuccessful result entries include `ErrorCode` and `ErrorMessage` values\. `ErrorCode` describes the type of error\. `ErrorMessage` provides more information about the error\. The following example has three result entries for a `PutEvents` request\. The second entry is unsuccessful\.

```
{
    "FailedEntryCount": 1, 
    "Entries": [
        {
            "EventId": "11710aed-b79e-4468-a20b-bb3c0c3b4860"
        },
        {   "ErrorCode": "InternalFailure",
            "ErrorMessage": "Internal Service Failure"
        },
        {
            "EventId": "d804d26a-88db-4b66-9eaf-9a11c708ae82"
        }
    ]
}
```

You can include entries that are unsuccessful in subsequent `PutEvents` requests\. First, to find out if there are failed entries in the request, check the `FailedRecordCount` parameter in `PutEventsResult`\. If it isn't zero, then you can add each `Entry` that has an `ErrorCode` that is not null to a subsequent request\. The following example shows a failure handler\.

```
PutEventsRequestEntry requestEntry = new PutEventsRequestEntry()
        .withTime(new Date())
        .withSource("com.mycompany.myapp")
        .withDetailType("myDetailType")
        .withResources("resource1", "resource2")
        .withDetail("{ \"key1\": \"value1\", \"key2\": \"value2\" }");

List<PutEventsRequestEntry> putEventsRequestEntryList = new ArrayList<>();
for (int i = 0; i < 3; i++) {
    putEventsRequestEntryList.add(requestEntry);
}

PutEventsRequest putEventsRequest = new PutEventsRequest();
putEventsRequest.withEntries(putEventsRequestEntryList);
PutEventsResult putEventsResult = awsEventsClient.putEvents(putEventsRequest);

while (putEventsResult.getFailedEntryCount() > 0) {
    final List<PutEventsRequestEntry> failedEntriesList = new ArrayList<>();
    final List<PutEventsResultEntry> PutEventsResultEntryList = putEventsResult.getEntries();
    for (int i = 0; i < PutEventsResultEntryList.size(); i++) {
        final PutEventsRequestEntry putEventsRequestEntry = putEventsRequestEntryList.get(i);
        final PutEventsResultEntry putEventsResultEntry = PutEventsResultEntryList.get(i);
        if (putEventsResultEntry.getErrorCode() != null) {
            failedEntriesList.add(putEventsRequestEntry);
        }
    }
    putEventsRequestEntryList = failedEntriesList;
    putEventsRequest.setEntries(putEventsRequestEntryList);
    putEventsResult = awsEventsClient.putEvents(putEventsRequest);
    }
```

## Sending events using the AWS CLI<a name="eb-send-events-aws-cli"></a>

You can use the AWS CLI to send custom events to EventBridge so they can be processed\. The following example puts one custom event into EventBridge:

```
aws events put-events \
--entries '[{"Time": "2016-01-14T01:02:03Z", "Source": "com.mycompany.myapp", "Resources": ["resource1", "resource2"], "DetailType": "myDetailType", "Detail": "{ \"key1\": \"value1\", \"key2\": \"value2\" }"}]'
```

You can also create a JSON file that contains custom events\.

```
[
  {
    "Time": "2016-01-14T01:02:03Z",
    "Source": "com.mycompany.myapp",
    "Resources": [
      "resource1",
      "resource2"
    ],
    "DetailType": "myDetailType",
    "Detail": "{ \"key1\": \"value1\", \"key2\": \"value2\" }"
  }
]
```

Then, to use the AWS CLI to read the entries from this file and send events, at a command prompt, type:

```
aws events put-events --entries file://entries.json
```