# Module 3 - Athena

## Background
In this module, you will learn how to use Amazon Athena to implement a serverless, interactive SQL query to analyze data in Amazon S3.

Although we can create a data catalog using the AWS Glue [crawler service](https://docs.aws.amazon.com/athena/latest/ug/glue-best-practices.html#schema-crawlers), we will look at creating the external table manually through SQL statements on the Athena console.

For instructions on using the Crawler service, please see Module 2.

## Implementation Instructions

Each of the following sections provides an implementation overview and detailed, step-by-step instructions. The overview should provide enough context for you to complete the implementation if you're already familiar with the AWS Management Console or you want to explore the services yourself without following a walkthrough.

# Step 1: Setup IAM Permissions for Athena <a name="rs-gsg-athena-step1"></a>

## The Data Source
We will use a public data set provided by [New York City Taxi rides](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml). Please see the data dictionary link within the previous link for more information.

### High-Level Instructions
Verify that the IAM user you're logged into the console has the following permissions. Skip to the next step if already have sufficient permissions.

+ AmazonAthenaFullAccess
+ AWSQuicksightAthenaAccess
+ AmazonS3FullAccess

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. Access the IAM console and select **Users**. Then select your username

1. If you already have **AdministratorAccess** policy associated with your account you can skip the permission steps.

1. Click **Add Permissions** button

![Add Permissions](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/select_user.png)

1. Select **Attach Existing Policies Directly**

![Attach policy](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/add_permission.png)

1. From the list of managed policies, attach the following:
    + AmazonAthenaFullAccess
    + AWSQuicksightAthenaAccess
    + AmazonS3FullAccess

</p></details>


# Step 2: Creating an Athena table <a name="rs-gsg-athena-step2"></a>

### High-Level Instructions
Create a new Athena database called `taxis` with a table schema referencing the data dictionary and the data in S3. The location of the data can be found in **s3://serverless-analytics/canonical/NY-Pub/**\.

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. Open the Athena console

1. Before we can create our table, lets first create a database by entering `CREATE DATABASE taxis` into the **Query Editor** box and clicking **Run Query**. Once created, make sure the `taxis` database is selected in the Database drop down on the left hand side.

1. Next we create our table schema. In the **Query Editor** box, enter the following and click **Run Query**

    ``` sql
    CREATE EXTERNAL TABLE taxis (
        vendorid STRING,
        pickup_datetime TIMESTAMP,
        dropoff_datetime TIMESTAMP,
        ratecode INT,
        passenger_count INT,
        trip_distance DOUBLE,
        fare_amount DOUBLE,
        total_amount DOUBLE,
        payment_type INT
        )
    PARTITIONED BY (YEAR INT, MONTH INT, TYPE string)
    STORED AS PARQUET
    LOCATION 's3://serverless-analytics/canonical/NY-Pub/'
    ```

1. Since this is a partitioned table, denoted by the PARTITIONED BY clause, we need to update the partitions.
Enter `MSCK REPAIR TABLE taxis` and click **Run Query**
</p></details>

1. Verify that all partitions were added by entering `SHOW PARTITIONS taxis` and click **Run Query**



### High-Level Instructions


<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>


</p></details>
