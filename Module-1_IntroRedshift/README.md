# Module 1 - Redshift

## Background
In this module, you will learn how to create a Redshift cluster, connect to it using a SQL client and load sample data to run some queries.

## Implementation Instructions

Each of the following sections provides an implementation overview and detailed, step-by-step instructions. The overview should provide enough context for you to complete the implementation if you're already familiar with the AWS Management Console or you want to explore the services yourself without following a walkthrough.

# Step 1: Set Up Prerequisites<a name="rs-gsg-prereq"></a>

### High-Level Instructions

Before you begin setting up an Amazon Redshift cluster, make sure that you complete the following prerequisites in this section: 

+ [Sign Up for AWS](#rs-gsg-prereq-signup)

+ [Install SQL Client Drivers and Tools](#rs-gsg-prereq-sql-client)

+ [Determine Firewall Rules](#rs-gsg-prereq-firewall-rules)


<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>


## Sign Up for AWS<a name="rs-gsg-prereq-signup"></a>

If you don’t already have an AWS account, you must sign up for one\. If you already have an account, you can skip this prerequisite and use your existing account\.

1. Open [https://aws\.amazon\.com/](https://aws.amazon.com/), and then choose **Create an AWS Account**\.
**Note**  
This might be unavailable in your browser if you previously signed into the AWS Management Console\. In that case, choose **Sign in to a different account**, and then choose **Create a new AWS account**\.

1. Follow the online instructions\.

    Part of the sign\-up procedure involves receiving a phone call and entering a PIN using the phone keypad\.

## Install SQL Client Drivers and Tools<a name="rs-gsg-prereq-sql-client"></a>

You can use most SQL client tools with Amazon Redshift JDBC or ODBC drivers to connect to an Amazon Redshift cluster\. In this tutorial, we show you how to connect using SQL Workbench/J, a free, DBMS\-independent, cross\-platform SQL query tool\. If you plan to use SQL Workbench/J to complete this tutorial, follow the steps below to get set up with the Amazon Redshift JDBC driver and SQL Workbench/J\. For more complete instructions for installing SQL Workbench/J, go to [Setting Up the SQL Workbench/J Client](http://docs.aws.amazon.com/redshift/latest/mgmt/connecting-using-workbench.html) in the *Amazon Redshift Cluster Management Guide*\. If you use an Amazon EC2 instance as your client computer, you will need to install SQL Workbench/J and the required drivers on the instance\.

**Note**  
You must install any third\-party database tools that you want to use with your clusters; Amazon Redshift does not provide or install any third\-party tools or libraries\.

### To Install SQL Workbench/J on Your Client Computer<a name="rs-gsg-how-to-install-sql-client-drivers-and-tools"></a>

1. Review the [SQL Workbench/J software license](http://www.sql-workbench.net/manual/license.html#license-restrictions)\.

1. Go to the [SQL Workbench/J website](http://www.sql-workbench.net/) and download the appropriate package for your operating system\.

1. Go to the [Installing and starting SQL Workbench/J page](http://www.sql-workbench.net/manual/install.html) and install SQL Workbench/J\.
**Important**  
Note the Java runtime version prerequisites for SQL Workbench/J and ensure you are using that version, otherwise, this client application will not run\.

1. Go to [Configure a JDBC Connection](http://docs.aws.amazon.com/redshift/latest/mgmt/configure-jdbc-connection.html) and download an Amazon Redshift JDBC driver to enable SQL Workbench/J to connect to your cluster\.

For more information about using the Amazon Redshift JDBC or ODBC drivers, see [Configuring Connections in Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/mgmt/configuring-connections.html)\.

## Determine Firewall Rules<a name="rs-gsg-prereq-firewall-rules"></a>

As part of this tutorial, you will specify a port when you launch your Amazon Redshift cluster\. You will also create an inbound ingress rule in a security group to allow access through the port to your cluster\.

If your client computer is behind a firewall, you need to know an open port that you can use so you can connect to the cluster from a SQL client tool and run queries\. If you do not know this, you should work with someone who understands your network firewall rules to determine an open port in your firewall\. Though Amazon Redshift uses port 5439 by default, the connection will not work if that port is not open in your firewall\. Because you cannot change the port number for your Amazon Redshift cluster after it is created, make sure that you specify an open port that will work in your environment during the launch process\.

</p></details>

# Step 2: Create an IAM Role for Redshift<a name="rs-gsg-create-an-iam-role"></a>

For any operation that accesses data on another AWS resource, such as using a COPY command to load data from Amazon S3, your cluster needs permission to access the resource and the data on the resource on your behalf\. You provide those permissions by using AWS Identity and Access Management, either through an IAM role that is attached to your cluster or by providing the AWS access key for an IAM user that has the necessary permissions\. 

To best protect your sensitive data and safeguard your AWS access credentials, we recommend creating an IAM role and attaching it to your cluster\. For more information about providing access permissions, see [Permissions to Access Other AWS Resources](http://docs.aws.amazon.com/redshift/latest/dg/copy-usage_notes-access-permissions.html)\.

### High-Level Instructions

Create a new IAM role that enables Amazon Redshift to load data from Amazon S3 buckets (read only). Attach the IAM role to your Redshift cluster.


<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>


1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the left navigation pane, choose **Roles**\.

1. Choose **Create role**

1. In the **AWS Service** group, choose **Redshift\.** 

1. Under **Select your use case**, choose **Redshift \- Customizable** then choose **Next: Permissions**\.

1. On the **Attach permissions policies** page, choose **AmazonS3ReadOnlyAccess**, and then choose **Next: Review**\.

1. For **Role name**, type a name for your role\. For this tutorial, type `myRedshiftRole`\. 

1. Review the information, and then choose **Create Role**\.

1. Choose the role name for new role\.

1. Copy the **Role ARN** to your clipboard—this value is the Amazon Resource Name \(ARN\) for the role that you just created\. You will use that value when you use the COPY command to load data in [Step 6: Load Sample Data from Amazon S3](rs-gsg-create-sample-db.md)\.

1. Attach the new role to your cluster\. You can attach the role when you launch a new cluster or you can attach it to an existing cluster\. In the next step, you'll attach the role to a new cluster\.

</p></details>

# Step 3: Launch a Sample Amazon Redshift Cluster<a name="rs-gsg-launch-sample-cluster"></a>

**The cluster you'll launch will be live and incur the standard Amazon Redshift usage fees for the cluster until you delete it.** Please don't forget to clean up any resources after the Module.


### High-Level Instructions
Create a new `publicly accessible` Redshift cluster in a region of your choice. For the node configuration, choose a `dc2.large` and `single node` cluster type with `1 compute node`.


<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.
**Important**  
If you use IAM user credentials, ensure that the user has the necessary permissions to perform the cluster operations\. For more information, go to [Controlling Access to IAM Users](http://docs.aws.amazon.com/redshift/latest/mgmt/iam-redshift-user-mgmt.html) in the *Amazon Redshift Cluster Management Guide*\.

1. In the main menu, select the region in which you want to create the cluster\. For the purposes of this tutorial, select **Sydney**.  

1. On the Amazon Redshift Dashboard, choose **Launch Cluster**\.

The Amazon Redshift Dashboard looks similar to the following:  
![Redshift Console](http://docs.aws.amazon.com/redshift/latest/gsg/images/rs-gsg-clusters-launch-cluster-10.png)

1. On the Cluster Details page, enter the following values and then choose **Continue**:

   + **Cluster Identifier**: type `<yourName>-cluster`

   + **Database Name**: leave this box blank\. Amazon Redshift will create a default database named `dev`\

   + **Database Port**: type the port number on which the database will accept connections\. You should have determined the port number in the prerequisite step of this tutorial\. You cannot change the port after launching the cluster, so make sure that you have an open port number in your firewall so that you can connect from SQL client tools to the database in the cluster\.

   + **Master User Name**: type `masteruser`\. You will use this username and password to connect to your database after the cluster is available\.

   + **Master User Password** and **Confirm Password**: type a password for the master user account\.  
![Cluster Config](http://docs.aws.amazon.com/redshift/latest/gsg/images/rs-gsg-clusters-launch-cluster-wizard-10.png)

1. On the Node Configuration page, select the following values and then choose **Continue**:

   + **Node Type**: **dc2\.large**

   + **Cluster Type**: **Single Node**  
![Node Configuration](http://docs.aws.amazon.com/redshift/latest/gsg/images/rs-gsg-clusters-launch-cluster-wizard-20.png)


1. Use the following values if you are launching your cluster in the EC2\-VPC platform:

   + **Cluster Parameter Group**: select the default parameter group\.

   + **Encrypt Database**: **None**\.

   + **Choose a VPC**: **Default VPC \(vpc\-xxxxxxxx\)**

   + **Cluster Subnet Group**: **default**

   + **Publicly Accessible**: **Yes**

   + **Choose a Public IP Address**: **No**

   + **Enhanced VPC Routing**: **No**

   + **Availability Zone**: **No Preference**

   + **VPC Security Groups**: **default \(sg\-xxxxxxxx\)**

   + **Create CloudWatch Alarm**: **No**

1. Associate an IAM role with the cluster

   For **AvailableRoles**, choose **myRedshiftRole** (configured in Step 2) and then choose **Continue**\.  
![Redshift IAM Role](http://docs.aws.amazon.com/redshift/latest/gsg/images/rs-gsg-clusters-launch-cluster-wizard-45.png)

1. On the Clusters page, choose the cluster that you just launched and review the **Cluster Status** information\. Make sure that the **Cluster Status** is **available** and the **Database Health** is **healthy** before you try to connect to the database later in this tutorial\.  
![Redshift Health Console](http://docs.aws.amazon.com/redshift/latest/gsg/images/rs-gsg-clusters-config-cluster-status.png)

</p></details>