# Monitor Host-Based Intrusion Detection System Alerts on Amazon EC2 Instances

![dashboard](https://github.com/awslabs/hids-cloudwatchlogs-elasticsearch-template/blob/master/images/hids-dashboard.png)

The [full post for this solution](https://aws.amazon.com/blogs/security/how-to-monitor-host-based-intrusion-detection-system-alerts-on-amazon-ec2-instances/) is available on the AWS Security Blog.

### Solution Overview
This solution uses [Amazon CloudWatch Logs](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) to collect and aggregate alerts in near real time from an open-source security ([OSSEC](http://ossec.github.io/)) HIDS. It uses a [CloudWatch Logs Subscription](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Subscriptions.html) to deliver the alerts to Amazon [Elasticsearch Service](https://aws.amazon.com/elasticsearch-service/) (Amazon ES) for analysis and visualization with [Kibana](https://en.wikipedia.org/wiki/Kibana).

This solution contains a test environment AWS CloudFormation stack that you can automatically provision into an existing Amazon VPC subnet. The CloudFormation template performs the following high-level steps in the region you choose:

1.	Creates two EC2 instances running [Amazon Linux](https://aws.amazon.com/amazon-linux-ami/) with an [AWS Identity and Access Management (IAM)](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html) role for CloudWatch Logs access. Note: In order to provide sample HIDS alert data, the two EC2 instances are configured automatically to generate simulated HIDS alerts locally.
2.	Installs and configures OSSEC, the [CloudWatch Logs agent](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/QuickStartEC2Instance.html), and additional packages used for the test environment.
3.	Creates the target HIDS Amazon ES domain.
4.	Creates the target HIDS CloudWatch Logs group.
5.	Creates the Lambda function and CloudWatch Logs Subscription in order to send HIDS alerts to Amazon ES.

### How the Solution Works
1.	On the target EC2 instances, the OSSEC HIDS generates alerts that the CloudWatch Logs agent captures. The HIDS performs log analysis, integrity checking, Windows registry monitoring, rootkit detection, realtime alerting, and active response. For more information, see [Getting started with OSSEC](http://ossec.github.io/docs/manual/non-technical-overview.html).
2.	The CloudWatch Logs group receives the alerts as events.
3.	A CloudWatch Logs subscription is applied to the target log group to forward the events through AWS Lambda to Amazon ES.
4.	Amazon ES loads the logged alert data.
5.	Kibana visualizes the alerts in near real time. Kibana is a popular open-source visualization tool designed to work with Amazon ES, which provides a default installation of Kibana with every Amazon ES domain.

![diagram](https://github.com/awslabs/hids-cloudwatchlogs-elasticsearch-template/blob/master/images/hids-cwl-es.png)

Note that this solution depends on Amazon ES and Lambda that, at the time of this writing, are available in the following regions: US East (N. Virginia), US East (Ohio), US West (Oregon), US West (N. California), EU (Ireland), EU (Frankfurt), Asia Pacific (Singapore), Asia Pacific (Tokyo), Asia Pacific (Sydney), and Asia Pacific (Seoul).

For the following input parameters, you must identify a target VPC and subnet (which requires Internet access) for deployment. If the target subnet uses an Internet gateway, set the AssignPublicIP parameter to true. If the target subnet uses a NAT gateway, you can leave the default setting of AssignPublicIP as false.
In order for the Lambda function to be deployed, you will need to stage the Lambda deployment package in an S3 bucket located in the region you are deploying into. To do this, you will download the zipped deployment package and upload it to your in-region bucket. The Lambda zipped deployment package can be [downloaded here](https://github.com/awslabs/hids-cloudwatchlogs-elasticsearch-template/raw/master/lambda-consumer/deployment-package/hids-lambda-consumer.zip). For additional information about uploading objects to S3, please reference [Uploading Object into Amazon S3 in our documentation](http://docs.aws.amazon.com/AmazonS3/latest/UG/UploadingObjectsintoAmazonS3.html).

You also must provide a trusted source IP address or CIDR block for access to the environment following the creation of the stack and an EC2 key pair to associate with the instances. You can find information about creating an EC2 key pair in the documentation here. Note that the trusted IP address or CIDR block also is used to create the Amazon ES access policy automatically for Kibana access. It is recommended that you utilize a specific IP address or CIDR range rather than using 0.0.0.0/0 which will allow all IPv4 addresses to access your instances.

### CloudFormation Parameters
![cf-params](https://github.com/awslabs/hids-cloudwatchlogs-elasticsearch-template/blob/master/images/cf-params.png)

1.	HIDSInstanceSize:
EC2 instance size for test server
2.	ESInstanceSize:
Elasticsearch instance size
3.	MyKeyPair:
A public/private key pair that allows you to connect securely to your instance after it launches
4.  MyS3Bucket:
In region S3 bucket with Lambda zipped deployment package
5.	MyS3Key:
In region S3 key for Lambda zipped deployment package
6.	VPCId:
An Amazon VPC into which to deploy the solution
7.	SubnetId:
A SubnetId with outbound connectivity within the VPC you selected (requires Internet access)
8.	AssignPublicIP:
Set to true if your subnet is configured to connect through an Internet gateway; set to false if your subnet is configured to connect through a NAT gateway
9.	MyTrustedNetwork:
Your trusted source IP or CIDR block that is used to whitelist access to the EC2 instances and the Amazon ES endpoint


If you already have a VPC Subnet configured with internet access, you can launch the stack:

[launch-stack]:https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=HIDS-Alerts-ES-Test-Stack&templateURL=https://s3.amazonaws.com/awsiammedia/public/sample/HIDSAlerts/hids-cwl-es.template

[![Launch Stack](https://github.com/awslabs/hids-cloudwatchlogs-elasticsearch-template/blob/master/images/launch-stack.png)][launch-stack]

### Thanks
Thanks to the [OSSEC Project Team](http://ossec.github.io/about.html#ossec-team) for authoring the OSSEC HIDS.

### License
[https://aws.amazon.com/asl/](https://aws.amazon.com/asl/)
