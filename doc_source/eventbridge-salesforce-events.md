# Receiving events from Salesforce<a name="eventbridge-salesforce-events"></a>

You can use Amazon EventBridge to receive events from Salesforce by configuring a flow in [Amazon AppFlow](https://aws.amazon.com/appflow/) that uses Salesforce as a data source\. AppFlow then sends Salesforce events EventBridge using a [partner event bus](create-partner-event-bus.md)\.

Events received from Salesforce via Amazon AppFlow are encapsulated in a standard EventBridge event envelope\. The following example displays a Salesforce event received by an EventBridge partner event bus\.

```
{
    "version": "0",
    "id": "5c42b99e-e005-43b3-c744-07990c50d2cc",
    "detail-type": "AccountChangeEvent",
    "source": "aws.partner/appflow.test/salesforce.com/364228160620/CustomSF-Source-Final",
    "account": "000000000",
    "time": "2020-08-20T18:25:51Z",
    "region": "us-west-2",
    "resources": [],
    "detail": {
        "ChangeEventHeader": {
            "commitNumber": 248197218874,
            "commitUser": "0056g000003XW7AAAW",
            "sequenceNumber": 1,
            "entityName": "Account",
            "changeType": "UPDATE",
            "changedFields": [
                "LastModifiedDate",
                "Region__c"
            ],
            "changeOrigin": "com/salesforce/api/soap/49.0;client=SfdcInternalAPI/",
            "transactionKey": "000035af-b239-0581-9f14-461e4187de11",
            "commitTimestamp": 1597947935000,
            "recordIds": [
                "0016g00000MLhLeAAL"
            ]
        },
        "LastModifiedDate": "2020-08-20T18:25:35.000Z",
        "Region__c": "America"
    }
}
```

## Configure AppFlow to use Salesforce as a partner event source<a name="eventbridge-configure-appflow"></a>

To send events to EventBridge, you first need to configure AppFlow to use Salesforce as a partner event source\.

1. In the [Amazon AppFlow console](https://console.aws.amazon.com/appflow/), choose **Create flow**\.

1. In the **Flow details** section, in the **Flow name** field, specify a name for your flow\.

1. Optionally, enter a description for the flow, and then choose **Next**\.

1. Under **Source details**, choose *Salesforce* from the **Source name** drop\-down, and then choose **Connect** to create a new connection\.

1. On the **Connect to Salesforce** dialog, choose either **Production** or **Sandbox** for the Salesforce environment\.

1. In the **Connection name** field, enter a unique name for the connection, and then choose **Continue**\.

   A Salesforce log in dialog box displays\.

1. In the Salesforce dialog box, enter your Salesforce username and password to log in to Salesforce\.

1. Select Salesforce events for the types of data for AppFlow to process\.

1. In the **Choose Salesforce event** drop\-down, select the type of event to send to EventBridge\.

1. Select **Amazon EventBridge** as a destination\.

1. Select **Create new partner event source** from the drop\-down\.

1. \(Optional\) Specify a unique suffix for the partner event source\.

1. Choose **Generate partner event source**\.

1. Choose an Amazon S3 bucket to store event payload files that are larger than 256 KB\.

1. In the **Flow trigger** section, ensure that **Run flow on event** is selected\. This setting ensures that the flow is executed when a new Salesforce event occurs\.

1. Choose **Next**\.

1. For field mapping, select **Map all fields directly**\. Alternatively, you can select the fields that are of interest from the **Source field name** list\.

   To learn more about field mapping, see [Map data fields](https://docs.aws.amazon.com/appflow/latest/userguide/getting-started.html#map-fields)\.

1. Choose **Next**\.

1. \(Optional\) Configure filters for data fields in Amazon AppFlow\.

1. Choose **Next**\.

1. Review the settings and choose **Create flow**\.

With the flow configured, Amazon AppFlow creates a new partner event source that you then need to associate with a partner event bus in your account\.

## Configure EventBridge to receive Salesforce events<a name="eventbridge-salesforce-events"></a>

Ensure that the Amazon AppFlow flow that is triggered from Salesforce events with EventBridge as a destination is configured before following instructions in this section\. 

**To configure EventBridge to receive Salesforce events**

1. Open the [Partner event sources](https://console.aws.amazon.com/events/home?#/partners) page in the EventBridge console\. 

1. Select the Salesforce partner event source that you created using the previous procedure in this topic\.

1. Choose **Associate with event bus**\.

1. Validate the name of the partner event bus\. 

1. Choose **Associate**\. 

1. In the Amazon AppFlow console, open the flow you created and choose **Activate flow**\.

1. Open the [Rules](https://console.aws.amazon.com/events/home?#/rules) page in the EventBridge console\.

1. Choose **Create rule**\.

1. Enter a unique name for the rule\.

1. Choose **Event pattern** in the **Define pattern** section\.

1. In the **Event matching pattern** section select **Pre\-defined pattern by service**\.

1. In the **Service provider** section select **All Events**\. This option ensures that all events sent to the partner event bus are included\.

1. In the **Select event bus** section, choose **Custom or partner event bus**\.

1. Select the newly\-created partner event bus, that was associated with the Amazon AppFlow partner event source\.

1. Select a target for the rule\. This is the service that is invoked when the rule is triggered\. One rule can have up to five targets\.

1. Choose **Create**\.

With the active rule, the destination service will receive all Salesforce events configured for your account\. If you need to filter the kinds of events that you want to process, or send different events to different targets, you can use [content\-based filtering with event patterns](content-filtering-with-event-patterns.md)\. 

**Note**  
For events larger than 256KB, Amazon AppFlow does not send the full event to EventBridge\. Instead, full event is put into an S3 bucket in your account, and the event payload sent to EventBridge includes a pointer to the Amazon S3 bucket\. You can use the pointer to get the full event from the bucket\.