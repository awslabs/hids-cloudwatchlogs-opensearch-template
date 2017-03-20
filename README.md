# Monitor EC2 Host Based Intrusion Detection Alerts with OSSEC, CloudWatch Logs, Lambda and Elasticsearch

### Solution Overview
This solution uses [Amazon CloudWatch Logs](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) to collect and aggregate alerts in near real time from an open-source security ([OSSEC](http://ossec.github.io/)) HIDS. It uses a [CloudWatch Logs Subscription](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Subscriptions.html) to deliver the alerts to Amazon [Elasticsearch Service](https://aws.amazon.com/elasticsearch-service/) (Amazon ES) for analysis and visualization with [Kibana](https://en.wikipedia.org/wiki/Kibana).

This solution contains a test environment AWS CloudFormation stack that you can automatically provision into an existing Amazon VPC subnet. The CloudFormation template performs the following high-level steps in the region you choose:

1.	Creates two EC2 instances running [Amazon Linux](https://aws.amazon.com/amazon-linux-ami/) with an [AWS Identity and Access Management (IAM)](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html) role for CloudWatch Logs access. Note: In order to provide sample HIDS alert data, the two EC2 instances are configured automatically to generate simulated HIDS alerts locally.
2.	Installs and configures OSSEC, the [CloudWatch Logs agent](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/QuickStartEC2Instance.html), and additional packages used for the test environment.
3.	Creates the target HIDS Amazon ES domain.
4.	Creates the target HIDS CloudWatch Logs group.
5.	Creates the Lambda function and CloudWatch Logs Subscription in order to send HIDS alerts to Amazon ES.

![diagram](https://github.com/awslabs/hids-cloudwatchlogs-elasticsearch-template/blob/master/images/hids-cwl-es.png)

Note that this solution depends on Amazon ES and Lambda that, at the time of this writing, are available in the following regions: US East (N. Virginia), US East (Ohio), US West (Oregon), US West (N. California), EU (Ireland), EU (Frankfurt), Asia Pacific (Singapore), Asia Pacific (Tokyo), Asia Pacific (Sydney), and Asia Pacific (Seoul).

If you already have a VPC Subnet configured with internet access, you can launch the stack:

[launch-stack]:https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=HIDS-Alerts-ES-Test-Stack&templateURL=https://s3.amazonaws.com/hids-cwl-es-artifacts/hids-cwl-es.template

[![Launch Stack](https://github.com/awslabs/hids-cloudwatchlogs-elasticsearch-template/blob/master/images/launch-stack.png)][launch-stack]

### How the Solution Works
1.	On the target EC2 instances, the OSSEC HIDS generates alerts that the CloudWatch Logs agent captures. The HIDS performs log analysis, integrity checking, Windows registry monitoring, rootkit detection, realtime alerting, and active response. For more information, see [Getting started with OSSEC](http://ossec.github.io/docs/manual/non-technical-overview.html).
2.	The CloudWatch Logs group receives the alerts as events.
3.	A CloudWatch Logs subscription is applied to the target log group to forward the events through AWS Lambda to Amazon ES.
4.	Amazon ES loads the logged alert data.
5.	Kibana visualizes the alerts in near real time. Kibana is a popular open-source visualization tool designed to work with Amazon ES, which provides a default installation of Kibana with every Amazon ES domain.

![dashboard](https://github.com/awslabs/hids-cloudwatchlogs-elasticsearch-template/blob/master/images/hids-dashboard.png)

### Thanks
Thanks to the [OSSEC Project Team](http://ossec.github.io/about.html#ossec-team) for authoring the OSSEC HIDS.

### License
[https://aws.amazon.com/asl/](https://aws.amazon.com/asl/)
