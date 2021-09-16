# Using Amazon EventBridge with Interface VPC Endpoints<a name="eb-related-service-vpc"></a>

If you use Amazon Virtual Private Cloud \(Amazon VPC\) to host your AWS resources, you can establish a private connection between your VPC and EventBridge\. Your resources on your VPC can use this connection to communicate with EventBridge\.

With a VPC, you have control over your network settings, such as the IP address range, subnets, route tables, and network gateways\. To connect your VPC to EventBridge, you define an *interface VPC endpoint* for EventBridge\. The endpoint provides reliable, scalable connectivity to EventBridge without requiring an internet gateway, network address translation \(NAT\) instance, or VPN connection\. For more information, see [What is Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/) in the *Amazon VPC User Guide*\.

Interface VPC endpoints are powered by AWS PrivateLink, which enables private communication between AWS services using an elastic network interface with private IP addresses\. For more information, see [AWS PrivateLink and VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/endpoint-services-overview.html)\.

When you use a private interface VPC endpoint, custom [events](eb-events.md) your VPC sends to EventBridge use that endpoint\. EventBridge then sends those events to other AWS services based on the [rules](eb-rules.md) and [targets](eb-targets.md) that you've configured\. Once events are sent to another service you can receive them through either the public endpoint or a VPC endpoint for that service\. For example, if you create a rule to send events to an Amazon SQS queue, you can configure an interface VPC endpoint for Amazon SQS to receive messages from that queue in your VPC without using the public endpoint\. 

## Availability<a name="eb-interface-VPC-availability"></a>

EventBridge currently supports VPC endpoints in the following Regions:
+ US East \(Ohio\)
+ US East \(N\. Virginia\)
+ US West \(N\. California\)
+ US West \(Oregon\)
+ Africa \(Cape Town\)
+ Asia Pacific \(Mumbai\)
+ Asia Pacific \(Hong Kong\)
+ Asia Pacific \(Seoul\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ Asia Pacific \(Osaka\)
+ Canada \(Central\)
+ Europe \(Frankfurt\)
+ Europe \(Ireland\)
+ Europe \(London\)
+ Europe \(Milan\)
+ Europe \(Paris\)
+ Europe \(Stockholm\)
+ South America \(São Paulo\)

## Creating a VPC Endpoint for EventBridge<a name="eb-create-VPC-endpoint"></a>

To use EventBridge with your VPC, create an interface VPC endpoint for EventBridge and choose **com\.amazonaws\.*Region*\.events** as the service name\. For more information, see [Creating an Interface Endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint.html) in the *Amazon VPC User Guide*\.