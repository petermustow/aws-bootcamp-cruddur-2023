# Week 0 — Billing and Architecture
The first week, getting excited to learn more and get into labbing along the way.

## Billing

 
**NOTE:**
- IAM account must be assigned the Billing role to access billing.
 - By default a root account has access to billing.
- N. Virginia region is required for anything billing.

**Role:** arn:aws:iam::aws:policy/job-function/Billing

**Description:** Grants permissions for billing and cost management. This includes viewing account usage and viewing and modifying budgets and payment methods.

## Installing AWS CLI

I'm running Windows 11 and I use Windows Package Manager (winget) to manage my installed application. Winget allows me to ensure that I am able to run a auto update script on my machine on login which will query all the app version against their repo(s) and then update then to the latest version to esure tha latest versions are installed.

/assets/week0/aws-client-install-2023-03-26_17-35-19.png

## Create a billing alert

**Link:** https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-estimatedcharges-alarm/

**Tips:**

- 10 free alarms.
- Billing alerts are created from within CloudWatch

## Create an alarm configuration as a JSON file:

    $ cat alarm_config.json
    
    {
    
    "AlarmName": "My Cloud",
    
    "AlarmDescription": "This alarm would be triggered if the daily estimated charges exceeds 50$",
    
    "ActionsEnabled": true,
    
    "AlarmActions": [
    
    "arn:aws:sns:<REGION>:<ACCOUNT_ID>:<SNS_TOPIC_NAME>"
    
    ],
    
    "EvaluationPeriods": 1,
    
    "DatapointsToAlarm": 1,
    
    "Threshold": 50,
    
    "ComparisonOperator": "GreaterThanOrEqualToThreshold",
    
    "TreatMissingData": "breaching",
    
    "Metrics": [{
    
    "Id": "m1",
    
    "MetricStat": {
    
    "Metric": {
    
    "Namespace": "AWS/Billing",
    
    "MetricName": "EstimatedCharges",
    
    "Dimensions": [{
    
    "Name": "Currency",
    
    "Value": "AUD"
    
    }]
    
    },
    
    "Period": 86400,
    
    "Stat": "Maximum"
    
    },
    
    "ReturnData": false
    
    },
    
    {
    
    "Id": "e1",
    
    "Expression": "IF(RATE(m1)>0,RATE(m1)*86400,0)",
    
    "Label": "DailyEstimatedCharges",
    
    "ReturnData": true
    
    }]
    
    }

  

## Call the PutMetricAlarm API:

aws cloudwatch put-metric-alarm --cli-input-json file://alarm_config.json

<provide  sceenshot  of  gui>


# Create a budget

**Link:** https://docs.aws.amazon.com/cli/latest/reference/budgets/create-budget.html

**Tips:**
- 2 free budgets.

## To create a Cost and Usage budget
Creates a budget and, if included, notifications and subscribers.

#### Contents of budget.json:

    $ cat budget.json
    
    {
    
    "BudgetLimit": {
    
    "Amount": "100",
    
    "Unit": "AUD"
    
    },
    
    "BudgetName": "Cloud Project Bootcamp",
    
    "BudgetType": "COST",
    
    "CostFilters": {
    
    "TagKeyValue": [
    
    "user:Key$value1",
    
    "user:Key$value2"
    
    ]
    
    },
    
    "CostTypes": {
    
    "IncludeCredit": true,
    
    "IncludeDiscount": true,
    
    "IncludeOtherSubscription": true,
    
    "IncludeRecurring": true,
    
    "IncludeRefund": true,
    
    "IncludeSubscription": true,
    
    "IncludeSupport": true,
    
    "IncludeTax": true,
    
    "IncludeUpfront": true,
    
    "UseBlended": false
    
    },
    
    "TimePeriod": {
    
    "Start": 1477958399,
    
    "End": 3706473600
    
    },
    
    "TimeUnit": "MONTHLY"
    
    }  

#### Contents of notifications-with-subscribers.json:

    $ cat notifications-with-subscribers.json
    
    [
    
    {
    
    "Notification": {
    
    "ComparisonOperator": "GREATER_THAN",
    
    "NotificationType": "ACTUAL",
    
    "Threshold": 80,
    
    "ThresholdType": "PERCENTAGE"
    
    },
    
    "Subscribers": [
    
    {
    
    "Address": "peter@cloudprojectbootcamp.com",
    
    "SubscriptionType": "EMAIL"
    
    }
    
    ]
    
    }
    
    ]


#### Create budget command

    aws budgets create-budget \
    --account-id 111122223333 \
    --budget file://budget.json \
    --notifications-with-subscribers file://notifications-with-subscribers.json

# Manage tags

**Link:** https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-tags.html

Freeform text which allows resource to be grouped and tagged key value pair. You are then able to search tags to

## To add a tag to a resource via cli

    aws ec2 create-tags \
    --resources ami-1234567890abcdef0 --tags Key=env,Value=prod
   
## To add tags to multiple resources via cli

    aws ec2 create-tags \
    --resources ami-1a2b3c4d i-1234567890abcdef0 \
    --tags Key=webserver,Value= Key=env,Value=prod

<provide sceenshot of gui>

# AWS Cost Manager
This will outline dates and cost of reseouces that are used and this can be sorted by filters and inforation that is requried to drill into.

# Credits
AWS credits can be added to the account but entering the vouncher code and applying this. The credits shall then be visable and sometimes these credits can only be targetted to certain services.

# AWS Calculator
**Link:** https://calculator.aws/
This allows you to crunch the numbers on resources and build out costing for future project or services you may be wanting to create in your enviroment.

# Free Tier
This is a list of all the free services that you are able to leverage with your AWS account.

# Architecture
The following diagram has been created as required in Lucid Chart and has been shared.

https://lucid.app/lucidchart/d02dea50-6c56-4905-bb11-ec45480594d2/edit?invitationId=inv_2969bae4-e8b6-44b4-8dda-beadba7db1aa
