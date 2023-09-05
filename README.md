# Monitor Host-Based Intrusion Detection System Alerts on Amazon EC2 Instances

![dashboard](https://raw.githubusercontent.com/awslabs/hids-cloudwatchlogs-elasticsearch-template/master/images/hids-dashboard.png)

### Solution Overview
This solution uses [Amazon CloudWatch Logs](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) to collect and aggregate alerts in near real time from an open-source security ([OSSEC](http://ossec.github.io/)) HIDS. It uses a [CloudWatch Logs Subscription](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Subscriptions.html) to deliver the alerts to Amazon [OpenSearch Service](https://aws.amazon.com/opensearch-service/) for analysis and visualization with [OpenSearch Dashboards](https://docs.aws.amazon.com/opensearch-service/latest/developerguide/dashboards.html).

This solution contains a test environment AWS CloudFormation stack that you can automatically provision into an existing Amazon VPC subnet. The CloudFormation template performs the following high-level steps in the region you choose:

1.	Creates two EC2 instances running [Amazon Linux](https://aws.amazon.com/amazon-linux-ami/) with an [AWS Identity and Access Management (IAM)](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html) role for CloudWatch Logs access. Note: In order to provide sample HIDS alert data, the two EC2 instances are configured automatically to generate simulated HIDS alerts locally.
2.	Installs and configures OSSEC, the [CloudWatch Logs agent](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/QuickStartEC2Instance.html), and additional packages used for the test environment.
3.	Creates the target HIDS Amazon OpenSearch domain.
4.	Creates the target HIDS CloudWatch Logs group.
5.	Creates the Lambda function and CloudWatch Logs Subscription in order to send HIDS alerts to Amazon OpenSearch.

### How the Solution Works
1.	On the target EC2 instances, the OSSEC HIDS generates alerts that the CloudWatch Logs agent captures. The HIDS performs log analysis, integrity checking, Windows registry monitoring, rootkit detection, realtime alerting, and active response. For more information, see [Getting started with OSSEC](http://ossec.github.io/docs/manual/non-technical-overview.html).
2.	The CloudWatch Logs group receives the alerts as events.
3.	A CloudWatch Logs subscription is applied to the target log group to forward the events through AWS Lambda to Amazon OpenSearch.
4.	Amazon OpenSearch loads the logged alert data.
5.	OpenSearch visualizes the alerts in near real time. OpenSearch Dashboards is an open-source visualization tool designed to work with OpenSearch. Amazon OpenSearch Service provides an installation of OpenSearch Dashboards with every OpenSearch Service domain.

![diagram](https://github.com/awslabs/hids-cloudwatchlogs-elasticsearch-template/blob/master/images/hids-cwl-es.png)

For the following input parameters, you must identify a target VPC and subnet (which requires Internet access) for deployment. If the target subnet uses an Internet gateway, set the AssignPublicIP parameter to true. If the target subnet uses a NAT gateway, you can leave the default setting of AssignPublicIP as false.

You also must provide a trusted source IP address or CIDR block for access to the environment following the creation of the stack and an EC2 key pair to associate with the instances. You can find information about creating an EC2 key pair in the documentation here. Note that the trusted IP address or CIDR block also is used to create the access policy automatically for dashboard access. It is recommended that you utilize a specific IP address or CIDR range rather than using 0.0.0.0/0 which will allow all IPv4 addresses to access your instances.

### CloudFormation Parameters

1.	HIDSInstanceSize:
EC2 instance size for test server
2.	OpenSearchInstanceSize:
OpenSearch instance size
In region S3 key for Lambda zipped deployment package
3.	VPCId:
An Amazon VPC into which to deploy the solution
4.	SubnetId:
A SubnetId with outbound connectivity within the VPC you selected (requires Internet access)
4.	AssignPublicIP:
Set to true if your subnet is configured to connect through an Internet gateway; set to false if your subnet is configured to connect through a NAT gateway
5.	MyTrustedNetwork:
Your trusted source IP or CIDR block that is used to whitelist access to the EC2 instances and the Amazon OpenSearch endpoint


If you already have a VPC Subnet configured with internet access, you can launch the stack using the [CloudFormation Template](https://raw.githubusercontent.com/awslabs/hids-cloudwatchlogs-elasticsearch-template/master/cloudformation/hids-cwl-opensearch.template).

### Thanks
Thanks to the [OSSEC Project Team](http://ossec.github.io/about.html#ossec-team) for authoring the OSSEC HIDS.

### License
[https://aws.amazon.com/asl/](https://aws.amazon.com/asl/)
