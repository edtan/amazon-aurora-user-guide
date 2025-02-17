# Sharing a DB Cluster Snapshot<a name="USER_ShareSnapshot"></a>

Using Amazon RDS, you can share a manual DB cluster snapshot in the following ways:
+ Sharing a manual DB cluster snapshot, whether encrypted or unencrypted, enables authorized AWS accounts to copy the snapshot\.
+ Sharing a manual DB cluster snapshot, whether encrypted or unencrypted, enables authorized AWS accounts to directly restore a DB cluster from the snapshot instead of taking a copy of it and restoring from that\.

**Note**  
To share an automated DB cluster snapshot, create a manual DB cluster snapshot by copying the automated snapshot, and then share that copy\.

For more information on copying a snapshot, see [Copying a Snapshot](USER_CopySnapshot.md)\. For more information on restoring a DB instance from a DB cluster snapshot, see [Restoring from a DB Cluster Snapshot](USER_RestoreFromSnapshot.md)\.

For more information on restoring a DB cluster from a DB cluster snapshot, see [Overview of Backing Up and Restoring an Aurora DB Cluster](Aurora.Managing.Backups.md)\.

You can share a manual snapshot with up to 20 other AWS accounts\. You can also share an unencrypted manual snapshot as public, which makes the snapshot available to all AWS accounts\. Take care when sharing a snapshot as public so that none of your private information is included in any of your public snapshots\. 

The following limitations apply when sharing manual snapshots with other AWS accounts:
+ When you restore a DB cluster from a shared snapshot using the AWS Command Line Interface \(AWS CLI\) or Amazon RDS API, you must specify the Amazon Resource Name \(ARN\) of the shared snapshot as the snapshot identifier\.

## Sharing an Encrypted Snapshot<a name="USER_ShareSnapshot.Encrypted"></a>

You can share DB cluster snapshots that have been encrypted "at rest" using the AES\-256 encryption algorithm, as described in [Encrypting Amazon Aurora Resources](Overview.Encryption.md)\. To do this, you must take the following steps:

1. Share the AWS Key Management Service \(AWS KMS\) encryption key that was used to encrypt the snapshot with any accounts that you want to be able to access the snapshot\.

   You can share AWS KMS encryption keys with another AWS account by adding the other account to the KMS key policy\. For details on updating a key policy, see [Key Policies](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS KMS Developer Guide*\. For an example of creating a key policy, see [Allowing Access to an AWS KMS Encryption Key](#USER_ShareSnapshot.Encrypted.KeyPolicy) later in this topic\.

1. Use the AWS Management Console, AWS CLI, or Amazon RDS API to share the encrypted snapshot with the other accounts\.

These restrictions apply to sharing encrypted snapshots:
+ You can't share encrypted snapshots as public\.
+ You can't share a snapshot that has been encrypted using the default AWS KMS encryption key of the AWS account that shared the snapshot\. 

### Allowing Access to an AWS KMS Encryption Key<a name="USER_ShareSnapshot.Encrypted.KeyPolicy"></a>

For another AWS account to copy an encrypted DB cluster snapshot shared from your account, the account that you share your snapshot with must have access to the KMS key that encrypted the snapshot\. To allow another AWS account access to an AWS KMS key, update the key policy for the KMS key with the ARN of the AWS account that you are sharing to as a `Principal` in the KMS key policy, and then allow the `kms:CreateGrant` action\.

After you have given an AWS account access to your KMS encryption key, to copy your encrypted snapshot, that AWS account must create an AWS Identity and Access Management \(IAM\) user if it doesn’t already have one\. In addition, that AWS account must also attach an IAM policy to that IAM user that allows the IAM user to copy an encrypted DB cluster snapshot using your KMS key\. The account must be an IAM user and cannot be a root AWS account identity due to KMS security restrictions\. 

In the following key policy example, user `111122223333` is the owner of the KMS encryption key, and user `444455556666` is the account that the key is being shared with\. This updated key policy gives the AWS account access to the KMS key by including the ARN for the root AWS account identity for user `444455556666` as a `Principal` for the policy, and by allowing the `kms:CreateGrant` action\. 

```
{
  "Id": "key-policy-1",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Allow use of the key",
      "Effect": "Allow",
      "Principal": {"AWS": [
        "arn:aws:iam::111122223333:user/KeyUser",
        "arn:aws:iam::444455556666:root"
      ]},
      "Action": [
        "kms:CreateGrant",
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:DescribeKey"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow attachment of persistent resources",
      "Effect": "Allow",
      "Principal": {"AWS": [
        "arn:aws:iam::111122223333:user/KeyUser",
        "arn:aws:iam::444455556666:root"
      ]},
      "Action": [
        "kms:CreateGrant",
        "kms:ListGrants",
        "kms:RevokeGrant"
      ],
      "Resource": "*",
      "Condition": {"Bool": {"kms:GrantIsForAWSResource": true}}
    }
  ]
}
```

#### Creating an IAM Policy to Enable Copying of the Encrypted Snapshot<a name="USER_ShareSnapshot.Encrypted.KeyPolicy.IAM"></a>

Once the external AWS account has access to your KMS key, the owner of that AWS account can create a policy that allows an IAM user created for that account to copy an encrypted snapshot encrypted with that KMS key\.

The following example shows a policy that can be attached to an IAM user for AWS account `444455556666` that enables the IAM user to copy a shared snapshot from AWS account `111122223333` that has been encrypted with the KMS key `c989c1dd-a3f2-4a5d-8d96-e793d082ab26` in the `us-west-2` region\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowUseOfTheKey",
            "Effect": "Allow",
            "Action": [
                "kms:Encrypt",
                "kms:Decrypt",
                "kms:ReEncrypt*",
                "kms:GenerateDataKey*",
                "kms:DescribeKey",
                "kms:CreateGrant",
                "kms:RetireGrant"
            ],
            "Resource": ["arn:aws:kms:us-west-2:111122223333:key/c989c1dd-a3f2-4a5d-8d96-e793d082ab26"]
        },
        {
            "Sid": "AllowAttachmentOfPersistentResources",
            "Effect": "Allow",
            "Action": [
                "kms:CreateGrant",
                "kms:ListGrants",
                "kms:RevokeGrant"
            ],
            "Resource": ["arn:aws:kms:us-west-2:111122223333:key/c989c1dd-a3f2-4a5d-8d96-e793d082ab26"],
            "Condition": {
                "Bool": {
                    "kms:GrantIsForAWSResource": true
                }
            }
        }
    ]
}
```

For details on updating a key policy, see [Key Policies](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS KMS Developer Guide*\.

## Sharing a Snapshot<a name="USER_ShareSnapshot.Sharing"></a>

You can share a DB cluster snapshot using the AWS Management Console, the AWS CLI, or the RDS API\.

### AWS Management Console<a name="USER_ShareSnapshot.Console"></a>

Using the Amazon RDS console, you can share a manual DB cluster snapshot with up to 20 AWS accounts\. You can also use the console to stop sharing a manual snapshot with one or more accounts\.

**To share a manual DB cluster snapshot by using the Amazon RDS console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the manual snapshot that you want to share\.

1. For **Actions**, choose **Share Snapshot**\.

1. Choose one of the following options for **DB snapshot visibility**\.
   + If the source is unencrypted, choose **Public** to permit all AWS accounts to restore a DB cluster from your manual DB cluster snapshot, or choose **Private** to permit only AWS accounts that you specify to restore a DB cluster from your manual DB cluster snapshot\.
**Warning**  
If you set **DB snapshot visibility** to **Public**, all AWS accounts can restore a DB cluster from your manual DB cluster snapshot and have access to your data\. Do not share any manual DB cluster snapshots that contain private information as **Public**\.
   + If the source is encrypted, **DB snapshot visibility** is set as **Private** because encrypted snapshots can't be shared as public\.

1. For **AWS Account ID**, type the AWS account identifier for an account that you want to permit to restore a DB cluster from your manual snapshot, and then choose **Add**\. Repeat to include additional AWS account identifiers, up to 20 AWS accounts\.

   If you make an error when adding an AWS account identifier to the list of permitted accounts, you can delete it from the list by choosing **Delete** at the right of the incorrect AWS account identifier\.  
![\[Permit AWS accounts to restore a manual DB cluster snapshot\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/ShareSnapshot_add.png)

1. After you have added identifiers for all of the AWS accounts that you want to permit to restore the manual snapshot, choose **Save** to save your changes\.

**To stop sharing a manual DB cluster snapshot with an AWS account**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the manual snapshot that you want to stop sharing\.

1. Choose **Actions**, and then choose **Share Snapshot**\.

1. To remove permission for an AWS account, choose **Delete** for the AWS account identifier for that account from the list of authorized accounts\.  
![\[Permit AWS accounts to restore a manual DB cluster snapshot\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/ShareSnapshot_delete.png)

1. Choose **Save** to save your changes\.

### AWS CLI<a name="USER_ShareSnapshot.CLI"></a>

To share a DB cluster snapshot, use the `aws rds modify-db-cluster-snapshot-attribute`  command\. Use the `--values-to-add` parameter to add a list of the IDs for the AWS accounts that are authorized to restore the manual snapshot\. 

The following example permits two AWS account identifiers, `123451234512` and `123456789012`, to restore the DB cluster snapshot named `manual-cluster-snapshot1`, and removes the `all` attribute value to mark the snapshot as private\.

```
aws rds modify-db-cluster-snapshot-attribute \ 
--db-cluster-snapshot-identifier manual-cluster-snapshot1 \ 
--attribute-name restore \ 
--values-to-add '["111122223333","444455556666"]'
```

To remove an AWS account identifier from the list, use the `-- values-to-remove` parameter\. The following example prevents AWS account ID 444455556666 from restoring the snapshot\.

```
aws rds modify-db-cluster-snapshot-attribute \ 
--db-cluster-snapshot-identifier manual-cluster-snapshot1 \ 
--attribute-name restore \ 
--values-to-remove '["444455556666 "]'
```

### API<a name="USER_ShareSnapshot.API"></a>

You can also share a manual DB cluster snapshot with other AWS accounts by using the Amazon RDS API\. To do so, call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterSnapshotAttribute.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterSnapshotAttribute.html) operation\. Specify `restore` for `AttributeName`, and use the `ValuesToAdd` parameter to add a list of the IDs for the AWS accounts that are authorized to restore the manual snapshot\. 

To make a manual snapshot public and restorable by all AWS accounts, use the value `all`\. However, take care not to add the `all` value for any manual snapshots that contain private information that you don't want to be available to all AWS accounts\. Also, don't specify `all` for encrypted snapshots, because making such snapshots public isn't supported\.

To remove sharing permission for an AWS account, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterSnapshotAttribute.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterSnapshotAttribute.html) operation with `AttributeName` set to `restore` and the `ValuesToRemove` parameter\. To mark a manual snapshot as private, remove the value `all` from the values list for the `restore` attribute\.

To list all of the AWS accounts permitted to restore a snapshot, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterSnapshotAttributes.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterSnapshotAttributes.html) API operation\.