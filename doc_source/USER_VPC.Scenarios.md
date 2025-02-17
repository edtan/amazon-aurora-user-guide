# Scenarios for Accessing a DB Instance in a VPC<a name="USER_VPC.Scenarios"></a>

Amazon Aurora supports the following scenarios for accessing a DB instance:
+ [An EC2 Instance in the Same VPC](#USER_VPC.Scenario1)
+ [An EC2 Instance in a Different VPC](#USER_VPC.Scenario3)
+ [An EC2 Instance Not in a VPC](#USER_VPC.ClassicLink)
+ [A Client Application Through the Internet](#USER_VPC.Scenario4)

## A DB Instance in a VPC Accessed by an EC2 Instance in the Same VPC<a name="USER_VPC.Scenario1"></a>

A common use of a DB instance in a VPC is to share data with an application server that is running in an EC2 instance in the same VPC\. This is the user scenario created if you use AWS Elastic Beanstalk to create an EC2 instance and a DB instance in the same VPC\. 

The following diagram shows this scenario\.

![\[VPC and EC2 security group Scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/con-VPC-sec-grp.png)

The simplest way to manage access between EC2 instances and DB instances in the same VPC is to do the following:
+ Create a VPC security group for your DB instances to be in\. This security group can be used to restrict access to the DB instances\. For example, you can create a custom rule for this security group that allows TCP access using the port you assigned to the DB instance when you created it and an IP address you use to access the DB instance for development or other purposes\.
+ Create a VPC security group for your EC2 instances \(web servers and clients\) to be in\. This security group can, if needed, allow access to the EC2 instance from the Internet via the VPC's routing table\. For example, you can set rules on this security group to allow TCP access to the EC2 instance over port 22\.
+ Create custom rules in the security group for your DB instances that allow connections from the security group you created for your EC2 instances\. This would allow any member of the security group to access the DB instances\.

For a tutorial that shows you how to create a VPC with both public and private subnets for this scenario, see [Tutorial: Create an Amazon VPC for Use with a DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. 

**To create a rule in a VPC security group that allows connections from another security group, do the following:**

1.  Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc](https://console.aws.amazon.com/vpc)\. 

1.  In the navigation pane, choose **Security Groups**\. 

1. Select or create a security group for which you want to allow access to members of another security group\. In the scenario preceding, this is the security group that you use for your DB instances\. Choose the **Inbound Rules** tab, and then choose **Edit rule**\.

1. On the **Edit inbound rules** page, choose **Add Rule**\.

1. From **Type**, choose one of the **All ICMP** options\. In the **Source** box, start typing the ID of the security group; this provides you with a list of security groups\. Select the security group with members that you want to have access to the resources protected by this security group\. In the scenario preceding, this is the security group that you use for your EC2 instance\.

1. Repeat the steps for the TCP protocol by creating a rule with **All TCP** as the **Type** and your security group in the **Source** box\. If you intend to use the UDP protocol, create a rule with **All UDP** as the **Type** and your security group in the **Source** box\. 

1. Create a custom TCP rule that permits access via the port you used when you created your DB instance, such as port 3306 for MySQL\. Enter your security group or an IP address to use in the **Source** box\.

1. Choose **Save** when you are done\.

![\[adding a security group to another security group's rules\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/con-vpc-add-sg-rule.png)

## A DB Instance in a VPC Accessed by an EC2 Instance in a Different VPC<a name="USER_VPC.Scenario3"></a>

 When your DB instance is in a different VPC from the EC2 instance you are using to access it, you can use VPC peering to access the DB instance\.

The following diagram shows this scenario\. 

![\[A DB Instance in a VPC Accessed by an EC2 Instance in a Different VPC\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/RDSVPC2EC2VPC.png)

A VPC peering connection is a networking connection between two VPCs that enables you to route traffic between them using private IP addresses\. Instances in either VPC can communicate with each other as if they are within the same network\. You can create a VPC peering connection between your own VPCs, with a VPC in another AWS account, or with a VPC in a different AWS Region\. To learn more about VPC peering, see [VPC Peering](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-peering.html) in the *Amazon Virtual Private Cloud User Guide*\. 

## A DB Instance in a VPC Accessed by an EC2 Instance Not in a VPC<a name="USER_VPC.ClassicLink"></a>

You can communicate between an Amazon Aurora DB instance that is in a VPC and an EC2 instance that is not in an Amazon VPC by using *ClassicLink*\. When you use Classic Link, an application on the EC2 instance can connect to the DB instance by using the endpoint for the DB instance\. ClassicLink is available at no charge\. 

The following diagram shows this scenario\. 

![\[A DB Instance in a VPC Accessed by an EC2 Instance Not in a VPC\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/ClassicLink.png)

Using ClassicLink, you can connect an EC2 instance to a logically isolated database where you define the IP address range and control the access control lists \(ACLs\) to manage network traffic\. You don't have to use public IP addresses or tunneling to communicate with the DB instance in the VPC\. This arrangement provides you with higher throughput and lower latency connectivity for inter\-instance communications\. 

**To enable ClassicLink between a DB instance in a VPC and an EC2 instance not in a VPC**

1.  Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc](https://console.aws.amazon.com/vpc)\. 

1.  In the navigation pane, choose **Your VPCs**\. 

1.  Choose the VPC used by the DB instance\. 

1.  In **Actions**, choose **Enable ClassicLink**\. In the confirmation dialog box, choose **Yes, Enable**\. 

1.  On the EC2 console, select the EC2 instance you want to connect to the DB instance in the VPC\. 

1.  In **Actions**, choose **ClassicLink**, and then choose **Link to VPC**\. 

1.  On the **Link to VPC** page, choose the security group you want to use, and then choose **Link to VPC**\. 

**Note**  
 The ClassicLink features are only visible in the consoles for accounts and regions that support EC2\-Classic\. For more information, see [ ClassicLink](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html) in the *Amazon EC2 User Guide for Linux Instances\.* 

## A DB Instance in a VPC Accessed by a Client Application Through the Internet<a name="USER_VPC.Scenario4"></a>

To access a DB instance in a VPC from a client application through the internet, you configure a VPC with a single public subnet, and an Internet gateway to enable communication over the Internet\. 

The following diagram shows this scenario\. 

![\[A DB Instance in a VPC Accessed by a Client Application Through the Internet\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/GS-VPC network.png)

We recommend the following configuration:
+ A VPC of size /16 \(for example CIDR: 10\.0\.0\.0/16\)\. This size provides 65,536 private IP addresses\.
+ A subnet of size /24 \(for example CIDR: 10\.0\.0\.0/24\)\. This size provides 256 private IP addresses\.
+ An Amazon Aurora DB instance that is associated with the VPC and the subnet\. Amazon RDS assigns an IP address within the subnet to your DB instance\.
+ An Internet gateway which connects the VPC to the Internet and to other AWS products\.
+ A security group associated with the DB instance\. The security group's inbound rules allow your client application to access to your DB instance\.

For information about creating a DB instance in a VPC, see [Creating a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.InstanceInVPC)\.