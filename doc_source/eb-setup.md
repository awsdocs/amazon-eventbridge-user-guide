# Amazon EventBridge setup and prerequisites<a name="eb-setup"></a>

To use Amazon EventBridge, you need an AWS account\. Your account allows you to use services such as Amazon EC2 to generate events that you can see in the EventBridge console\. You can also install and configure the AWS Command Line Interface \(AWS CLI\) to use a command\-line interface to see events\.

**Topics**
+ [Sign up for Amazon Web Services \(AWS\)](#eb-sign-up)
+ [Sign in to the Amazon EventBridge console](#eb-console-signin)
+ [Account credentials](#eb-manage-account-credentials)
+ [Set up the AWS Command Line Interface](#eb-set-up-cli-eventbridge)
+ [Regional Endpoints](#eb-prerequisites)

## Sign up for Amazon Web Services \(AWS\)<a name="eb-sign-up"></a>

When you create an account, we automatically sign up your account for all AWS services, but you only pay for the services that you use\. 

If you already have an account, you can skip this step\. If you don't have an account, use the following procedure to create one\.

**To sign up for an account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

   When you sign up for an AWS account, an *AWS account root user* is created\. The root user has access to all AWS services and resources in the account\. As a security best practice, [assign administrative access to an administrative user](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html), and use only the root user to perform [tasks that require root user access](https://docs.aws.amazon.com/accounts/latest/reference/root-user-tasks.html)\.

## Sign in to the Amazon EventBridge console<a name="eb-console-signin"></a>

**To sign in to the Amazon EventBridge console**
+ Sign in to the AWS Management Console and open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

## Account credentials<a name="eb-manage-account-credentials"></a>

Although you can use your root user credentials to access EventBridge, we recommend that you use an AWS Identity and Access Management \(IAM\) account instead\. If you're using an IAM account to access EventBridge, you must have the following permissions\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "events:*"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:events:*:*:*"
    },
    {
      "Action": [
        "iam:PassRole"
      ],
      "Effect": "Allow",
      "Resource": "*",
      "Condition": {
        "StringLike": {
          "iam:PassedToService": "events.amazonaws.com"
        }
      }
    }  
  ]
}
```

For more information, see [Authentication](eb-iam.md#eb-authentication)\.

## Set up the AWS Command Line Interface<a name="eb-set-up-cli-eventbridge"></a>

You can use the AWS CLI to perform EventBridge operations\.

For information about how to install and configure the AWS CLI, see [Getting Set Up with the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) in the *AWS Command Line Interface User Guide*\.

## Regional Endpoints<a name="eb-prerequisites"></a>

You must enable the default regional endpoints to use EventBridge\. For more information, see [Activating and Deactivating AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html) in the *IAM User Guide*\.