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

1. Copy the **Role ARN** to your clipboard—this value is the Amazon Resource Name \(ARN\) for the role that you just created\. You will use that value when you use the COPY command to load data in Step 6.

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


# Step 4: Authorize Access to the Cluster<a name="rs-gsg-authorize-cluster-access"></a>

In the previous step, you launched your Amazon Redshift cluster\. Before you can connect to the cluster, you need to configure a security group to authorize access: 


### High-Level Instructions
Whitelist your IP address to provide network access in your Redshift cluster's security group.


<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. In the Amazon Redshift console, in the navigation pane, choose **Clusters**\.

1. Choose `your cluster` to open it, and make sure you are on the **Configuration** tab\.

1. Under **Cluster Properties**, for **VPC Security Groups**, choose your security group\.  
![Security Group](http://docs.aws.amazon.com/redshift/latest/gsg/images/rs-gsg-clusters-config-vpc-security-group.png)

1. After your security group opens in the Amazon EC2 console, choose the **Inbound** tab\.  
![SG inbound](http://docs.aws.amazon.com/redshift/latest/gsg/images/rs-gsg-security-vpc-security-group-select.png)

1. Choose **Edit**, and enter the following, then choose **Save**: 

   + **Type**: **Custom TCP Rule**\.

   + **Protocol**: **TCP**\.

   + **Port Range**: type the same port number that you used when you launched the cluster\. The default port for Amazon Redshift is `5439`, but your port might be different\.

   + **Source**: select **My IP**
</p></details>

# Step 5: Connect to the Sample Cluster<a name="rs-gsg-connect-to-cluster"></a>

### High-Level Instructions

Connect to your Redshift cluster using your SQL client tool and run a simple query to test the connection.

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

+ [To Get Your Connection String](#rs-gsg-how-to-get-connection-string)

+ [To Connect from SQL Workbench/J to Your Cluster](#rs-gsg-how-to-connect-from-workbench)

### To Get Your Connection String<a name="rs-gsg-how-to-get-connection-string"></a>

1. In the Amazon Redshift console, in the navigation pane, choose **Clusters**\.

1. Choose `your-cluster` to open it, and make sure you are on the **Configuration** tab\.

1. On the **Configuration** tab, under **Cluster Database Properties**, copy the JDBC URL of the cluster\. 
**Note**  
The endpoint for your cluster is not available until the cluster is created and in the available state\.  
![JDBC URL](http://docs.aws.amazon.com/redshift/latest/gsg/images/rs-mgmt-clusters-cluster-database-properties-jdbc.png)

### To Connect from SQL Workbench/J to Your Cluster<a name="rs-gsg-how-to-connect-from-workbench"></a>

This step assumes you installed SQL Workbench/J in Step 1, otherwise use your application specific settings to connect to your cluster.

1. Open SQL Workbench/J\.

1. Choose **File**, and then choose **Connect window**\.

1. Choose **Create a new connection profile**\.

1. In the **New profile** text box, type a name for the profile\.

1. Choose **Manage Drivers**\. The **Manage Drivers** dialog opens\.

1. Choose the **Create a new entry** button\. In the **Name** text box, type a name for the driver\.  
![Driver](http://docs.aws.amazon.com/redshift/latest/gsg/images/jdbc-manage-drivers.png)

Choose the folder icon next to the **Library** box, navigate to the location of the driver, select it, and then choose **Open**\.  
![JDBC Driver](http://docs.aws.amazon.com/redshift/latest/gsg/images/redshift_jdbc_file.png)

If the **Please select one driver** dialog box displays, select **com\.amazon\.redshift\.jdbc4\.Driver** or **com\.amazon\.redshift\.jdbc41\.Driver** and choose **OK**\. SQL Workbench/J automatically completes the **Classname** box\. Leave the **Sample URL** box blank, and then choose **OK**\. 

1. In the **Driver** box, choose the driver you just added\.

1. In **URL**, copy the JDBC URL from the Amazon Redshift console and paste it here\.

1. In **Username**, type *masteruser*\.

1. In **Password**, type the password associated with the master user account\.

1. Choose the **Autocommit** box\. 

1. Choose the **Save profile list** icon, as shown below:  
![Profile](http://docs.aws.amazon.com/redshift/latest/gsg/images/sql_workbench_save.png)

1. Choose **OK**\.  
![Overall](http://docs.aws.amazon.com/redshift/latest/gsg/images/redshift_driver_sql_workbench.png)

</p></details>


# Step 6: Load Sample Data from Amazon S3<a name="rs-gsg-create-sample-db"></a>
At this point you have a database called `dev` and you are connected to it\. Now you will create some tables in the database, upload data to the tables, and try a query\. For your convenience, the sample data you will load is available in an Amazon S3 bucket\. 

### High-Level Instructions

Download all of the sample delimited data (7 x txt files) from s3://awssampledbuswest2/tickit/ and examine the data structure. Create a total of 7 new tables for the different data types and run some sample queries. 

Suggested queries:
   + Get definition for the sales table
   + Find total sales on a given calendar date
   + Find top 10 buyers by quantity
   + Find events in the 99.9 percentile in terms of all time gross sales

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>


**Note**  
Before you proceed, ensure that your SQL Workbench/J client is connected to the cluster\.


1. Create tables\.

   Copy and execute the following create table statements to create tables in the `dev` database\. For more information about the syntax, go to [CREATE TABLE](http://docs.aws.amazon.com/redshift/latest/dg/r_CREATE_TABLE_NEW.html) in the *Amazon Redshift Database Developer Guide*\.

    ``` sql
    create table users(
        userid integer not null distkey sortkey,
        username char(8),
        firstname varchar(30),
        lastname varchar(30),
        city varchar(30),
        state char(2),
        email varchar(100),
        phone char(14),
        likesports boolean,
        liketheatre boolean,
        likeconcerts boolean,
        likejazz boolean,
        likeclassical boolean,
        likeopera boolean,
        likerock boolean,
        likevegas boolean,
        likebroadway boolean,
        likemusicals boolean);

    create table venue(
        venueid smallint not null distkey sortkey,
        venuename varchar(100),
        venuecity varchar(30),
        venuestate char(2),
        venueseats integer);

    create table category(
        catid smallint not null distkey sortkey,
        catgroup varchar(10),
        catname varchar(10),
        catdesc varchar(50));
    
    create table date(
        dateid smallint not null distkey sortkey,
        caldate date not null,
        day character(3) not null,
        week smallint not null,
        month character(5) not null,
        qtr character(5) not null,
        year smallint not null,
        holiday boolean default('N'));
    
    create table event(
        eventid integer not null distkey,
        venueid smallint not null,
        catid smallint not null,
        dateid smallint not null sortkey,
        eventname varchar(200),
        starttime timestamp);
    
    create table listing(
        listid integer not null distkey,
        sellerid integer not null,
        eventid integer not null,
        dateid smallint not null  sortkey,
        numtickets smallint not null,
        priceperticket decimal(8,2),
        totalprice decimal(8,2),
        listtime timestamp);
    
    create table sales(
        salesid integer not null,
        listid integer not null distkey,
        sellerid integer not null,
        buyerid integer not null,
        eventid integer not null,
        dateid smallint not null sortkey,
        qtysold smallint not null,
        pricepaid decimal(8,2),
        commission decimal(8,2),
        saletime timestamp);
    ```

1.  Load sample data from Amazon S3 by using the COPY command\. 

    **Note**
    We recommend using the COPY command to load large datasets into Amazon Redshift from Amazon S3 or DynamoDB\. For more information about COPY syntax, see [COPY](http://docs.aws.amazon.com/redshift/latest/dg/r_COPY.html) in the *Amazon Redshift Database Developer Guide*\. 

    The sample data for this tutorial is provided in an Amazon S3 bucket that is owned by Amazon Redshift\. The bucket permissions are configured to allow all authenticated AWS users read access to the sample data files\. 

    To load the sample data, you must provide authentication for your cluster to access Amazon S3 on your behalf\. You can provide either role\-based authentication or key\-based authentication\. We recommend using role\-based authentication\. For more information about both types of authentication, see [CREDENTIALS](http://docs.aws.amazon.com/redshift/latest/dg/copy-parameters-credentials.html) in the Amazon Redshift Database Developer Guide\.

    For this step, you will provide authentication by referencing the IAM role you created and then attached to your cluster in previous steps\.
    **Note**  
    If you don’t have proper permissions to access Amazon S3, you receive the following error message when running the COPY command: `S3ServiceException: Access Denied`\.

    The COPY commands include a placeholder for the IAM role ARN, as shown in the following example\.

    ``` shell
    copy users from 's3://awssampledbuswest2/tickit/allusers_pipe.txt' 
    credentials 'aws_iam_role=<iam-role-arn>' 
    delimiter '|' region 'us-west-2';
    ```

    To authorize access using an IAM role, replace *<iam\-role\-arn>* in the CREDENTIALS parameter string with the role ARN for the IAM role you created in [Step 2: Create an IAM Role](rs-gsg-create-an-iam-role.md)\.

    Your COPY command will look similar to the following example\. 

    ``` shell
    copy users from 's3://awssampledbuswest2/tickit/allusers_pipe.txt' 
    credentials 'aws_iam_role=arn:aws:iam::123456789012:role/myRedshiftRole' 
    delimiter '|' region 'us-west-2';
    ```

    To load the sample data, replace *<iam\-role\-arn>* in the following COPY commands with your role ARN\. Then run the commands in your SQL client tool\.

    ``` shell
    copy users from 's3://awssampledbuswest2/tickit/allusers_pipe.txt' 
    credentials 'aws_iam_role=<iam-role-arn>' 
    delimiter '|' region 'us-west-2';

    copy venue from 's3://awssampledbuswest2/tickit/venue_pipe.txt' 
    credentials 'aws_iam_role=<iam-role-arn>' 
    delimiter '|' region 'us-west-2';

    copy category from 's3://awssampledbuswest2/tickit/category_pipe.txt' 
    credentials 'aws_iam_role=<iam-role-arn>' 
    delimiter '|' region 'us-west-2';

    copy date from 's3://awssampledbuswest2/tickit/date2008_pipe.txt' 
    credentials 'aws_iam_role=<iam-role-arn>' 
    delimiter '|' region 'us-west-2';

    copy event from 's3://awssampledbuswest2/tickit/allevents_pipe.txt' 
    credentials 'aws_iam_role=<iam-role-arn>' 
    delimiter '|' timeformat 'YYYY-MM-DD HH:MI:SS' region 'us-west-2';
    
    copy listing from 's3://awssampledbuswest2/tickit/listings_pipe.txt' 
    credentials 'aws_iam_role=<iam-role-arn>' 
    delimiter '|' region 'us-west-2';
    
    copy sales from 's3://awssampledbuswest2/tickit/sales_tab.txt'
    credentials 'aws_iam_role=<iam-role-arn>'
    delimiter '\t' timeformat 'MM/DD/YYYY HH:MI:SS' region 'us-west-2';
    ```

1. Now try the example queries\. For more information, go to [SELECT](http://docs.aws.amazon.com/redshift/latest/dg/r_SELECT_synopsis.html) in the *Amazon Redshift Developer Guide*\.

    ``` sql
    -- Get definition for the sales table.
    SELECT *    
    FROM pg_table_def    
    WHERE tablename = 'sales';    

    -- Find total sales on a given calendar date.
    SELECT sum(qtysold) 
    FROM   sales, date 
    WHERE  sales.dateid = date.dateid 
    AND    caldate = '2008-01-05';
    
    -- Find top 10 buyers by quantity.
    SELECT firstname, lastname, total_quantity 
    FROM   (SELECT buyerid, sum(qtysold) total_quantity
            FROM  sales
            GROUP BY buyerid
            ORDER BY total_quantity desc limit 10) Q, users
    WHERE Q.buyerid = userid
    ORDER BY Q.total_quantity desc;
    
    -- Find events in the 99.9 percentile in terms of all time gross sales.
    SELECT eventname, total_price 
    FROM  (SELECT eventid, total_price, ntile(1000) over(order by total_price desc) as percentile 
            FROM (SELECT eventid, sum(pricepaid) total_price
                    FROM   sales
                    GROUP BY eventid)) Q, event E
            WHERE Q.eventid = E.eventid
            AND percentile = 1
    ORDER BY total_price desc;
    ```

1. You can optionally go the Amazon Redshift console to review the queries you executed\. The **Queries** tab shows a list of queries that you executed over a time period you specify\. By default, the console displays queries that have executed in the last 24 hours, including currently executing queries\. 

   + Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

   + In the cluster list in the right pane, choose `your-cluster`\.

   + Choose the **Queries** tab\. 

    The console displays list of queries you executed as shown in the example below\.  
![queries](http://docs.aws.amazon.com/redshift/latest/gsg/images/cmdws-cluster-query-list.png)

   + To view more information about a query, choose the query ID link in the **Query** column or choose the magnifying glass icon\. 

    The following example shows the details of a query you ran in a previous step\.   
    ![query_result](http://docs.aws.amazon.com/redshift/latest/gsg/images/cmdws-cluster-query.png)

</p></details>