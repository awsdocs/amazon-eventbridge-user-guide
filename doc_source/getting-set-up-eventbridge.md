# Setting Up Amazon EventBridge<a name="getting-set-up-eventbridge"></a>

To use Amazon EventBridge you need an AWS account\. Your AWS account allows you to use services \(for example, Amazon EC2\) to generate events that you can view in the CloudWatch console, a web\-based interface\. In addition, you can install and configure the AWS Command Line Interface \(AWS CLI\) to use a command\-line interface\.

## Sign Up for Amazon Web Services \(AWS\)<a name="sign-up-eventbridge"></a>

When you create an AWS account, we automatically sign up your account for all AWS services\. You pay only for the services that you use\. 

If you have an AWS account already, skip to the next step\. If you don't have an AWS account, use the following procedure to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

## Sign in to the Amazon EventBridge Console<a name="console-signin-eventbridge"></a>

**To sign in to the Amazon EventBridge console**
+ Sign in to the AWS Management Console and open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

## Account Credentials<a name="manage-account-credentials"></a>

Although you can use your root user credentials to access EventBridge, we recommend that you use an AWS Identity and Access Management \(IAM\) account\. If you're using an IAM account to access EventBridge, you must have the following permissions:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "events:*",
        "iam:PassRole"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
```

For more information, see [Authentication](auth-and-access-control-eventbridge.md#authentication-eventbridge)\.

## Set Up the Command Line Interface<a name="set-up-cli-eventbridge"></a>

You can use the AWS CLI to perform EventBridge operations\.

For information about how to install and configure the AWS CLI, see [Getting Set Up with the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) in the *AWS Command Line Interface User Guide*\.

## Regional Endpoints<a name="eventbridge-prerequisites"></a>

You must enable regional endpoints \(the default\) in order to use EventBridge\. For more information, see [Activating and Deactivating AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html) in the *IAM User Guide*\.