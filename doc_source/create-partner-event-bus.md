# Receiving Events from an SaaS Partner<a name="create-partner-event-bus"></a>

To be able to receive events from SaaS partner applications and services, you must have a partner event source offered to you from the partner\. You can then create a partner event bus and match it to the corresponding partner event source\.

Partner event sources are available in the following Regions\.


| Code | Name | 
| --- | --- | 
| `us-east-1` | US East \(N\. Virginia\) | 
| `us-east-2` | US East \(Ohio\) | 
| `us-west-1` | US West \(N\. California\) | 
| `us-west-2` | US West \(Oregon\) | 
| `ca-central-1` | Canada \(Central\) | 
| `eu-central-1` | Europe \(Frankfurt\) | 
| `eu-west-1` | Europe \(Ireland\) | 
| `eu-west-2` | Europe \(London\) | 
| `eu-west-3` | Europe \(Paris\) | 
| `eu-north-1` | Europe \(Stockholm\) | 
| `ap-east-1` | Asia Pacific \(Hong Kong\) | 
| `ap-northeast-1` | Asia Pacific \(Tokyo\) | 
| `ap-northeast-2` | Asia Pacific \(Seoul\) | 
| `ap-southeast-1` | Asia Pacific \(Singapore\) | 
| `ap-southeast-2` | Asia Pacific \(Sydney\) | 
| `ap-south-1` | Asia Pacific \(Mumbai\) | 
| `sa-east-1` | South America \(São Paulo\) | 

**To begin to receive events from an SaaS partner**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Partner event sources**\.

1. Find the partner that you want and choose **Set up** for that partner\.

1. Choose **Copy** to copy your account ID to the clipboard\.

1. In the navigation pane, choose **Partner event sources**\.

1. Go to the partner's website and follow the instructions to create a partner event source\. Use your account ID for this\. The event source that you create will be available to only your account\.

1. Go back to the EventBridge console and choose **Partner event sources** in the navigation pane\.

1. Select the button next to the partner event source, and choose **Associate with event bus**\. 

   The status of that event source changes from `Pending` to `Active`, and the name of the event bus is updated to match the partner event source name\. You can now start creating rules that trigger on events from that partner event source\. For more information, see [Creating a Rule That Triggers on an SaaS Partner Event](create-rule-partner-events.md)\.