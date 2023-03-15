---
title: " Management & Governance Checklist "
date: 2021-05-20T11:16:08-04:00
chapter: false
weight: 1
pre: "<b>1. </b>"
---
 

## 1. CloudWatch

### General Spend Overview- Identifying CloudWatch spend

#### How to identify on Cost Explorer:

* Filter By Service-> **CloudWatch** and Group By -> **Usage Type**
![Images/cff_cw1.png](/Cost/CFF_CloudWatch/Images/cff_cw1.png?classes=lab_picture_small)

#### How to identify on Billing Console:

* On Billing Console, Search **Amazon CloudWatch**
![Images/cff_cw2.png](/Cost/CFF_CloudWatch/Images/cff_cw2.png?classes=lab_picture_small) 

### Metrics
Metrics are data about the performance of your systems. By default, many services provide free metrics for resources (such as Amazon EC2 instances, Amazon EBS volumes, and Amazon RDS DB instances). You can also enable detailed monitoring for some resources, such as your Amazon EC2 instances, or publish your own application metrics. Amazon CloudWatch can load all the metrics in your account (both AWS resource metrics and application metrics that you provide) for search, graphing, and alarms.

Metric data is kept for 15 months, enabling you to view both up-to-the-minute data and historical data.

#### How to identify on Cost Explorer:

* On Cost Explorer, Group by **Instance Type** and Filter by Service -> **RDS** and Instance Type -> **No Instance Type**
![Images/cff_rds2.png](/Cost/CFF_RDS/Images/cff_rds2.png?classes=lab_picture_small)

#### Best Practices:
* Metrics pricing is regional
* Cost changes depending by the num metrics/month and the API call requested
* Use GetMetricData API (Return ordered, paginated results) instead of GetMetricStatiscis 
* **Notice**- Third party monitoring tools can increase cost (frequent API calls)
* Identify most expensive Metrics
    * aws cloudwatch list-metrics —namespace 'AWS/SNS' —recently-active PT3H

* How to identify Custom Metrics on CW console-
	* **PutMetricData** calls for a custom metric- look if you have this kind of Usage Type
    * On Cloudwatch console → All metrics → here you can see two namespaces custom namespaces and aws namespaces, go to custom namespaces and see how many metrics you have per namespace. Identigu with metrics are being used per specific name space and verify they are needed
* EC2 detailed Monitoring
    * To reduce costs, turn off detailed monitoring of instances, Auto Scaling group launch configurations, or API gateways, as appropriate
    * Use the EC2 describe-instances API to identify the instances using Detailed Monitoring.
        * Possible states:
            * "Monitoring": {"State": "disabled"}
            * "Monitoring": {"State": ”enabled"}

### Logs
CloudWatch Logs enables you to centralize the logs from all of your systems, applications, and AWS services that you use


#### Best Practices:
* You pay for Collect (Data ingestion), Storage (Archival), Analyze (Logs insights queries)
* Best practice is to locate S3 logs buckets with same region where the service runs
* Identify log groups incurring highest charges
    * CUR query
![Images/cff_cw_query.png](/Cost/CFF_CloudWatch/Images/cff_cw_query.png?classes=lab_picture_small)

   * CloudWatch Logs Metrics
      * IncomingBytes metric shows how much data is being ingested in CloudWatch log groups in near-real time
* Optimizing log charges

    * Log retention- export to S3 based on retention policy
    * Archive log data 
    * Vended logs- sending Vended logs like VPC flow log to Amazon S3 is up to 50% cheaper then sending to CloudWatch logs


#### Alarms & Dashboards:

Amazon CloudWatch dashboards are customizable home pages in the CloudWatch console that you can use to monitor your resources in a single view, even those resources that are spread across different Regions. You can use CloudWatch dashboards to create customized views of the metrics and alarms for your AWS resource

Metric data is kept for 15 months, enabling you to view both up-to-the-minute data and historical data.

#### Best Practices:
* Charges are incurred when you exceed three dashboards (with up to 50 metrics)
* Reducing cost-
    * Delete unnecessary dashboards
    * Free Tier- up to 3 dashboards, up to 50 dashboards across all dashboards, make dashboard related API calls through console and not SDK or CLI
    * Check for unused Alarms (it is possible that the resource has been deleted but the alarm still exists. Good indicator is an alarm in INSUFFICIENT_DATA state.
    * Check that the Alarms have at least one alarm action by using AWS config
    * Enable High resolution Alarms only for specific mission critical use cases.


* Set metric alarm and composite alarm
    * Metric alarm- watches a single metric 
    * composite alarm- includes a rule expression that takes into account alarm state of other alarms (Alarm states- OK, ALARM, INSUFFINCET_DATA)
    * You can alarm status with DescribeAlarms API call
 


 ## 2. Using Service Control Policies


* Restrict [deployment to regions](https://aws.amazon.com/blogs/security/easier-way-to-control-access-to-aws-regions-using-iam-policies/) you don’t expect to deploy in. 
* Restrict access to our most [expensive EC2 instance families](https://blog.vizuri.com/limiting-allowed-aws-instance-type-with-iam-policy) (for example p-family GPU Instances or high memory x instances) with defined exceptions
* Restrict deployment on [previous generation of instances](https://wellarchitectedlabs.com/cost/200_labs/200_2_cost_and_usage_governance/3_ec2_restrict_family/) (for example, restrict m4 and earlier but allow m5)
* Restrict access to services with large upfront fees or commitments, for example our Snow Family, AWS Outputs and AWS Shield Advanced services
* Restrict access to [AWS MarketPlace](https://docs.aws.amazon.com/marketplace/latest/buyerguide/buyer-iam-users-groups-policies.html) or develop a private MarketPlace if you expect usage to occur.
* Restrict access to any commercial operating systems/database engines which aren’t expected to be used (for example, Oracle databases or RedHat Instances)
* Implement [Tagging Policies](https://wellarchitectedlabs.com/cost/100_labs/100_8_tag_policies/) to ensure any mandatory tags are using a consistent naming conventions.
* Use Service Control Policies to restrict deployment of infrastructure if the required tags aren’t included. For existing accounts, apply these rules in AWS Config so you don’t affect any infrastructure being automatically deployed but can notify owners if there are some non-compliant resources.
 


## 3. Governance
* Introduce Cloud Center of Excellences within each program to centrally share best practices not just in cost optimization but architecture, security and performance strategies
* Document every restriction you have implemented, who the owner of this restriction is, and a brief justification on why this is in place.
* [AWS Private Marketplace](https://aws.amazon.com/marketplace/features/privatemarketplace) enables administrators to build customized digital catalogs of approved products from AWS Marketplace. 
