# Receiving events from Salesforce<a name="eb-saas-salesforce"></a>

You can use Amazon EventBridge to receive [events](eb-events.md) from Salesforce in following ways:
+ By using Salesforce's Event Bus Relay feature to receive events directly on an EventBridge partner event bus\.
+ By configuring a flow in [Amazon AppFlow](https://aws.amazon.com/appflow/) that uses Salesforce as a data source\. Amazon AppFlow then sends Salesforce events to EventBridge by using a [partner event bus](eb-saas.md)\.

You can send event information to Salesforce using API destinations\. Once the event is sent to Salesforce, it can be processed by [Flows](https://help.salesforce.com/s/articleView?id=flow.htm) or [Apex triggers](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers.htm)\. For more information about setting up a Salesforce API destination, see [Tutorial: Create a connection to Salesforce as an API destination](eb-tutorial-salesforce.md)\.

**Topics**
+ [Receiving events from Salesforce using Event Bus Relay](#eb-saas-salesforce-relay)
+ [Receiving events from Salesforce using Amazon AppFlow](#eb-saas-salesforce-appflow)

## Receiving events from Salesforce using Event Bus Relay<a name="eb-saas-salesforce-relay"></a>

### Step 1: Set up Salesforce Event Bus Relay and an EventBridge partner event source<a name="eb-set-up-relay"></a>

When you create an event relay configuration on Salesforce, Salesforce creates a partner event source in EventBridge in the pending state\.

**To configure Salesforce Event Bus Relay**

1. [Set Up a REST API Tool](https://resources.docs.salesforce.com/rel1/doc/en-us/static/pdf/Salesforce_Event_Bus_Relay_Pilot.pdf#h.z63eim1tqkm3)

1. [\(Optional\) Define a Platform Event](https://resources.docs.salesforce.com/rel1/doc/en-us/static/pdf/Salesforce_Event_Bus_Relay_Pilot.pdf#h.2m5t2i52o23m)

1. [Create a Channel for a Custom Platform Event](https://resources.docs.salesforce.com/rel1/doc/en-us/static/pdf/Salesforce_Event_Bus_Relay_Pilot.pdf#h.s0spl5puf9d0)

1. [Create a Channel Member to Associate the Custom Platform Event](https://resources.docs.salesforce.com/rel1/doc/en-us/static/pdf/Salesforce_Event_Bus_Relay_Pilot.pdf#h.rdhi4awp8cvv)

1. [Create a Named Credential](https://resources.docs.salesforce.com/rel1/doc/en-us/static/pdf/Salesforce_Event_Bus_Relay_Pilot.pdf#h.etec44jyv3og)

1. [Create an Event Relay Configuration](https://resources.docs.salesforce.com/rel1/doc/en-us/static/pdf/Salesforce_Event_Bus_Relay_Pilot.pdf#h.43rfyeehz0w5)

### Step 2: Activate Salesforce partner event source in the EventBridge console and start the event relay<a name="eb-salesforce-activate-source"></a>

1. Open the [Partner event sources](https://console.aws.amazon.com/events/home?#/partners) page in the EventBridge console\. 

1. Select the Salesforce partner event source that you created in Step 1\.

1. Choose **Associate with event bus**\.

1. Validate the name of the partner event bus\.

1. Choose **Associate**\. 

1. [Start the Event Relay](https://resources.docs.salesforce.com/rel1/doc/en-us/static/pdf/Salesforce_Event_Bus_Relay_Pilot.pdf#h.t01b3xp87vhu)

Now that you've set up and started the Event Bus Relay and configured the partner event source you can create an [EventBridge rule that reacts to events](eb-create-rule.md) to filter and send the data to a [target](eb-targets.md)\.

## Receiving events from Salesforce using Amazon AppFlow<a name="eb-saas-salesforce-appflow"></a>

Amazon AppFlow encapsulates events from Salesforce in an EventBridge event envelope\. The following example shows a Salesforce event received by an EventBridge partner event bus\.

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

### Step 1: Configure Amazon AppFlow to use Salesforce as a partner event source<a name="eb-configure-appflow"></a>

To send events to EventBridge, you first need to configure Amazon AppFlow to use Salesforce as a partner event source\.

1. In the [Amazon AppFlow console](https://console.aws.amazon.com/appflow/), choose **Create flow**\.

1. In the **Flow details** section, in **Flow name** enter a name for your flow\.

1. \(Optional\) Enter a description for the flow and then choose **Next**\.

1. Under **Source details**, choose *Salesforce* from the **Source name** drop\-down, and then choose **Connect** to create a new connection\.

1. In the **Connect to Salesforce** dialog box, choose either **Production** or **Sandbox** for the Salesforce environment\.

1. In the **Connection name** field, enter a unique name for the connection, and then choose **Continue**\.

1. In the Salesforce dialog box, do the following:

   1. Enter your Salesforce username and password to log in to Salesforce\.

   1. Select Salesforce events for the types of data for Amazon AppFlow to process\.

1. In the **Choose Salesforce event** drop\-down, select the type of event to send to EventBridge\.

1. For a destination, select **Amazon EventBridge**\.

1. Select **Create new partner event source**\.

1. \(Optional\) Specify a unique suffix for the partner event source\.

1. Choose **Generate partner event source**\.

1. Choose an Amazon S3 bucket to store event payload files that are larger than 256 KB\.

1. In the **Flow trigger** section, ensure that **Run flow on event** is selected\. This setting ensures that the flow is executed when a new Salesforce event occurs\.

1. Choose **Next**\.

1. For field mapping, select **Map all fields directly**\. Alternatively, you can select the fields that are of interest from the **Source field name** list\.

   For more information about field mapping, see [Map data fields](https://docs.aws.amazon.com/appflow/latest/userguide/getting-started.html#map-fields)\.

1. Choose **Next**\.

1. \(Optional\) Configure filters for data fields in Amazon AppFlow\.

1. Choose **Next**\.

1. Review the settings and then choose **Create flow**\.

With the flow configured, Amazon AppFlow creates a new partner event source that you then need to associate with a partner event bus in your account\.

### Step 2: Configure EventBridge to receive Salesforce events<a name="eb-salesforce-events"></a>

Ensure that the Amazon AppFlow flow that is triggered from Salesforce events with EventBridge as a destination is configured before following instructions in this section\. 

**To configure EventBridge to receive Salesforce events**

1. Open the [Partner event sources](https://console.aws.amazon.com/events/home?#/partners) page in the EventBridge console\. 

1. Select the Salesforce partner event source that you created in Step 1\.

1. Choose **Associate with event bus**\.

1. Validate the name of the partner event bus\.

1. Choose **Associate**\. 

1. In the Amazon AppFlow console, open the flow you created and choose **Activate flow**\.

1. Open the [Rules](https://console.aws.amazon.com/events/home?#/rules) page in the EventBridge console\.

1. Choose **Create rule**\.

1. Enter a unique name for the rule\.

1. Choose **Event pattern** in the **Define pattern** section\.

1. For **Event matching pattern**, select **Pre\-defined pattern by service**\.

1. For **Service provider** section, select **All Events**\. 

1. For **Select event bus**, choose **Custom or partner event bus**\.

1. Select the event bus that you associated with the Amazon AppFlow partner event source\.

1. For **Select targets**, choose the AWS service that is to act when the rule runs\. One rule can have up to five targets\.

1. Choose **Create**\.

The target service receives all Salesforce events configured for your account\. To filter the events or send some events to different targets, you can use [content\-based filtering with event patterns](eb-event-patterns-content-based-filtering.md)\. 

**Note**  
For events larger than 256KB, Amazon AppFlow doesn't send the full event to EventBridge\. Instead, Amazon AppFlow puts the event into an S3 bucket in your account, and then sends an event to EventBridge with a pointer to the Amazon S3 bucket\. You can use the pointer to get the full event from the bucket\.