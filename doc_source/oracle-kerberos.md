# Using Kerberos authentication with Amazon RDS for Oracle<a name="oracle-kerberos"></a>

You can use Kerberos authentication to authenticate users when they connect to your Amazon RDS DB instance running Oracle\. 

**Topics**
+ [Overview of Kerberos authentication for Oracle DB instances](#oracle-kerberos-overview)
+ [Setting up Kerberos authentication for Oracle DB instances](oracle-kerberos-setting-up.md)
+ [Managing a DB instance in a domain](oracle-kerberos-managing.md)
+ [Connecting to Oracle with Kerberos authentication](oracle-kerberos-connecting.md)

## Overview of Kerberos authentication for Oracle DB instances<a name="oracle-kerberos-overview"></a>

In a Kerberos configuration, your DB instance works with AWS Directory Service for Microsoft Active Directory, also called AWS Managed Microsoft AD, to enable Kerberos authentication\. When users authenticate with an Oracle DB instance joined to the trusting domain, authentication requests are forwarded to the directory that you create with AWS Directory Service\.

Keeping all of your credentials in the same directory can save you time and effort\. You have a centralized place for storing and managing credentials for multiple database instances\. Using a directory can also improve your overall security profile\.

### Supported AWS Regions<a name="oracle-kerberos-overview.supported"></a>

Amazon RDS supports Kerberos authentication for Oracle DB instances in the following AWS Regions: 
+ US East \(Ohio\)
+ US East \(N\. Virginia\)
+ US West \(N\. California\)
+ US West \(Oregon\)
+ Asia Pacific \(Mumbai\)
+ Asia Pacific \(Seoul\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ Canada \(Central\)
+ Europe \(Frankfurt\)
+ Europe \(Ireland\)
+ Europe \(London\)
+ Europe \(Stockholm\)
+ South America \(São Paulo\)
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)

**Note**  
Kerberos authentication isn't supported for DB instance classes that are deprecated for Oracle DB instances\. For more information, see [DB instance class support for Oracle](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses)\.

### Workflow for Kerberos setup<a name="oracle-kerberos-overview.workflow"></a>

You must configure both the directory and the Oracle DB instance\.

**To set up Kerberos authentication for an Oracle DB instance**

1. Use AWS Managed Microsoft AD to create an AWS Managed Microsoft AD directory\. You can use the AWS Management Console, the AWS CLI, or the AWS Directory Service API to create the directory\. Make sure to open the relevant outbound ports on the directory security group so that the directory can communicate with the Oracle DB instance\.

1. Create an AWS Identity and Access Management \(IAM\) role that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess`\. The role allows Amazon RDS to make calls to your directory\.

   For the role to allow access, the AWS Security Token Service \(AWS STS\) endpoint must be activated in the correct AWS Region for your AWS account\. AWS STS endpoints are active by default in all AWS Regions, and you can use them without any further actions\. For more information, see [Activating and deactivating AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html#sts-regions-activate-deactivate) in the *IAM User Guide*\.

1. Create and configure users in the AWS Managed Microsoft AD directory using the Microsoft Active Directory tools\. For more information about creating users in your Microsoft Active Directory, see [Manage users and groups in AWS Managed Microsoft AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_manage_users_groups.html) in the *AWS Directory Service Administration Guide*\.

1. If you plan to locate the directory and the DB instance in different AWS accounts or virtual private clouds \(VPCs\), configure VPC peering\. For more information, see [What is VPC peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) in the *Amazon VPC Peering Guide*\.

1. Create or modify an Oracle DB instance either from the console, CLI, or RDS API using one of the following methods:
   + [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)
   + [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)
   + [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)
   + [Restoring a DB instance to a specified time](USER_PIT.md)

   You can locate the DB instance in the same Amazon Virtual Private Cloud \(VPC\) as the directory or in a different AWS account or VPC\. When you create or modify the DB instance, do the following:
   + Provide the domain identifier \(`d-*` identifier\) that was generated when you created your directory\.
   + Provide the name of the IAM role that you created\.
   + Ensure that the DB instance security group can receive inbound traffic from the directory security group\.

1. Use the Amazon RDS master user credentials to connect to the Oracle DB instance\. Create the user in Oracle to be identified externally\. Externally identified users can log in to the Oracle DB instance using Kerberos authentication\.

To get Kerberos authentication using an on\-premises or self\-hosted Microsoft Active Directory, create a forest trust or external trust\. The trust can be one\-way or two\-way\. For more information on setting up forest trusts using AWS Directory Service, see [ When to create a trust relationship](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/setup_trust.html) in the *AWS Directory Service Administration Guide*\.