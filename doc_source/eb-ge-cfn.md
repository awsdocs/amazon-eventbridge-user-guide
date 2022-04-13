# AWS CloudFormation template for setting up the Route 53 health check<a name="eb-ge-cfn"></a>

When using global endpoints you have to have a Route 53 health check to monitor the status of your Regions\. The following template defines a [Amazon CloudWatch alarm](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-cw-alarm.html) and uses it to define a [Route 53 health check](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-route53-healthcheck.html)\.

**Topics**
+ [AWS CloudFormation template for defining a Route 53 health check](#eb-ge-cfn-template)
+ [CloudWatch alarm template properties](#eb-ge-cfn-cw-alarm-definitions)
+ [Route 53 health check template properties](#eb-ge-cfn-health-check-definitions)

## AWS CloudFormation template for defining a Route 53 health check<a name="eb-ge-cfn-template"></a>

Use the following template to define your Route 53 health check\.

```
Description: |-
  Global endpoints health check that will fail when the average Amazon EventBridge 
  latency is above 30 seconds for a duration of 5 minutes. Note, missing data will 
  cause the health check to fail, so if you only send events intermittently, consider 
  changing the heath check to use a longer evaluation period or instead treat missing 
  data as 'missing' instead of 'breaching'.

Metadata:
  AWS::CloudFormation::Interface:
    ParameterGroups: 
      - Label: 
          default: "Global endpoint health check alarm configuration"
        Parameters:
          - HealthCheckName
          - HighLatencyAlarmPeriod
          - MinimumEvaluationPeriod
          - MinimumThreshold
          - TreatMissingDataAs
    ParameterLabels:
      HealthCheckName:
        default: Health check name
      HighLatencyAlarmPeriod:
        default: High latency alarm period
      MinimumEvaluationPeriod:
        default: Minimum evaluation period
      MinimumThreshold:
        default: Minimum threshold
      TreatMissingDataAs:
        default: Treat missing data as

Parameters:
  HealthCheckName:
    Description: Name of the health check
    Type: String
    Default: LatencyFailuresHealthCheck
  HighLatencyAlarmPeriod:
    Description: The period, in seconds, over which the statistic is applied. Valid values are 10, 30, 60, and any multiple of 60.
    MinValue: 10
    Type: Number
    Default: 60
  MinimumEvaluationPeriod:
    Description: The number of periods over which data is compared to the specified threshold. You must have at least one evaluation period.
    MinValue: 1
    Type: Number
    Default: 5
  MinimumThreshold:
    Description: The value to compare with the specified statistic.
    Type: Number
    Default: 30000
  TreatMissingDataAs:
    Description: Sets how this alarm is to handle missing data points.
    Type: String
    AllowedValues:
      - breaching
      - notBreaching
      - ignore
      - missing
    Default: breaching  

Mappings:
  "InsufficientDataMap":
    "missing":
      "HCConfig": "LastKnownStatus"
    "breaching":
      "HCConfig": "Unhealthy"  

Resources:
  HighLatencyAlarm:
      Type: AWS::CloudWatch::Alarm
      Properties:
        AlarmDescription: High Latency in Amazon EventBridge
        MetricName: IngestionToInvocationStartLatency
        Namespace: AWS/Events
        Statistic: Average
        Period: !Ref HighLatencyAlarmPeriod
        EvaluationPeriods: !Ref MinimumEvaluationPeriod
        Threshold: !Ref MinimumThreshold
        ComparisonOperator: GreaterThanThreshold
        TreatMissingData: !Ref TreatMissingDataAs

  LatencyHealthCheck:
      Type: AWS::Route53::HealthCheck
      Properties:
        HealthCheckTags:
          - Key: Name
            Value: !Ref HealthCheckName
        HealthCheckConfig:
          Type: CLOUDWATCH_METRIC
          AlarmIdentifier:
            Name:
              Ref: HighLatencyAlarm
            Region: !Ref AWS::Region
          InsufficientDataHealthStatus: !FindInMap [InsufficientDataMap, !Ref TreatMissingDataAs, HCConfig]

Outputs:
  HealthCheckId:
    Description: The identifier that Amazon Route 53 assigned to the health check when you created it.
    Value: !GetAtt LatencyHealthCheck.HealthCheckId
```

Event IDs can change across API calls so correlating events across Regions requires you to have an immutable, unique identifier\. Consumers should also be designed with idempotency in mind\. That way, if you're replicating events, or replaying them from archives, there are no side effects from the events being processed in both Regions\.

## CloudWatch alarm template properties<a name="eb-ge-cfn-cw-alarm-definitions"></a>

**Note**  
For all **editable** fields, consider your throughput per second\. If you only send events intermittently, consider changing the heath check to use a longer evaluation period or instead treat missing data as `missing` instead of `breaching`\. 

The following properties are used in th CloudWatch alarm section of the template:


| Metric | Description | 
| --- | --- | 
|  `AlarmDescription`  |  The description of the alarm\. Default: **High Latency in Amazon EventBridge**  | 
|  `MetricName`  |  The name of the metric associated with the alarm\. This is required for an alarm based on a metric\. For an alarm based on a math expression, you use `Metrics` instead and you can't specify `MetricName`\. Default: IngestionToInvocationStartLatency  | 
|  `Namespace`  |  The namespace of the metric associated with the alarm\. This is required for an alarm based on a metric\. For an alarm based on a math expression, you can't specify `Namespace` and you use `Metrics` instead\. Default: `AWS/Events`  | 
|  `Statistic`  |  The statistic for the metric associated with the alarm, other than percentile\. Default: Average  | 
|  `Period`  |  The period, in seconds, over which the statistic is applied\. This is required for an alarm based on a metric\. Valid values are 10, 30, 60, and any multiple of 60\. Default: **60**  | 
|  `EvaluationPeriods`  |  The number of periods over which data is compared to the specified threshold\. If you are setting an alarm that requires that a number of consecutive data points be breaching to trigger the alarm, this value specifies that number\. If you are setting an "M out of N" alarm, this value is the N, and `DatapointsToAlarm` is the M\. Default: **5**  | 
|  `Threshold`  |  The value to compare with the specified statistic\. Default: **30,000**  | 
|  `ComparisonOperator`  |  The arithmetic operation to use when comparing the specified statistic and threshold\. The specified statistic value is used as the first operand\. Default: `GreaterThanThreshold`  | 
|  `TreatMissingData`  |  Sets how this alarm is to handle missing data points\. Valid values: `breaching`, `notBreaching`, `ignore`, and `missing` Default: `breaching`  | 

## Route 53 health check template properties<a name="eb-ge-cfn-health-check-definitions"></a>

**Note**  
For all **editable** fields, consider your throughput per second\. If you only send events intermittently, consider changing the heath check to use a longer evaluation period or instead treat missing data as `missing` instead of `breaching`\. 

The following properties are used in th Route 53 health check section of the template:


| Metric | Description | 
| --- | --- | 
|  `HealthCheckName`  |  The name of the health check\. Default: **LatencyFailuresHealthCheck**  | 
|  `InsufficientDataHealthStatus`  |  When CloudWatch has insufficient data about the metric to determine the alarm state, the status that you want Amazon Route 53 to assign to the health check Valid values: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-ge-cfn.html) Default: Unhealthy This field is updated based on the input to the `TreatMissingData` field\. If `TreatingMissingData` is set to `Missing`, it will be updated to `LastKnownStatus`\.If `TreatingMissingData` is set to `Breaching`, it will be updated to `Unhealthy`\.  | 