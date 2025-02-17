# Enabling Network Communication from Amazon Aurora MySQL to Other AWS Services<a name="AuroraMySQL.Integrating.Authorizing.Network"></a>

To invoke AWS Lambda functions or access files from Amazon S3, the network configuration of your Aurora DB cluster must allow outbound connections to endpoints for those services\. Aurora returns the following error messages if it can't connect to a service endpoint\.

```
ERROR 1871 (HY000): S3 API returned error: Network Connection
```

```
ERROR 1873 (HY000): Lambda API returned error: Network Connection. Unable to connect to endpoint
```

If you encounter these messages while invoking AWS Lambda functions or accessing files from Amazon S3, check if your Aurora DB cluster is public or private\. If your Aurora DB cluster is private, you must configure it to enable connections\.

For an Aurora DB cluster to be public, it must be marked as publicly accessible\. If you look at the details for the DB cluster in the AWS Management Console, **Publicly Accessible** is **Yes** if this is the case\. The DB cluster must also be in an Amazon VPC public subnet\. For more information about publicly accessible DB instances, see [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\. For more information about public Amazon VPC subnets, see [Your VPC and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html)\.

If your Aurora DB cluster isn’t publicly accessible and in a VPC public subnet, it is private\. You might have a DB cluster that is private and want to invoke AWS Lambda functions or access Amazon S3 files\. If so, configure the cluster so it can connect to Internet addresses through Network Address Translation \(NAT\)\. As an alternative for Amazon S3, you can instead configure the VPC to have a VPC endpoint for Amazon S3 associated with the DB cluster’s route table\. For more information about configuring NAT in your VPC, see [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)\. For more information about configuring VPC endpoints, see [VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)\. 