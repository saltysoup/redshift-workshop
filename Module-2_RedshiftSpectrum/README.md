# Module 2 - Redshift Spectrum

## Background
In this module, you will learn how to use AWS Glue to create a data catalog, and use Redshift Spectrum to query data in Amazon S3.

## Implementation Instructions

Each of the following sections provides an implementation overview and detailed, step-by-step instructions. The overview should provide enough context for you to complete the implementation if you're already familiar with the AWS Management Console or you want to explore the services yourself without following a walkthrough.

# Step 1: Set Up IAM Role Prerequisites<a name="rs-gsg-prereq"></a>

## References
1. [Setting up IAM Permissions for AWS Glue](http://docs.aws.amazon.com/glue/latest/dg/getting-started-access.html)
1. [Setting up IAM Permissions for Amazon Redshift Spectrum](http://docs.aws.amazon.com/redshift/latest/dg/c-spectrum-iam-policies.html)

### High-Level Instructions

Create 2 x new IAM roles for the AWS Glue and Redshift service to interact with your S3 resource. The suggested mananaged polices for the Glue role are `AWSGlueServiceRole`, `AWSGlueServiceNotebookRole` and `AmazonS3FullAccess`. The suggested managed policies for the Redshift role are `AWSGlueServiceRole`, `AmazonS3ReadOnlyAccess` and `AmazonAthenaFullAccess`.

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

## Setup IAM Permissions for AWS Glue
1. Access the IAM console and select **Users**. Then select your username

1. Click **Add Permissions** button

1. From the list of managed policies, attach the following:

    + AWSGlueConsoleFullAccess
    + CloudWatchLogsReadOnlyAccess
    + AWSCloudFormationReadOnlyAccess

## Setup AWS Glue default service role
1. From the IAM console click **Roles** and create a new role

1. Select **Glue** from the list of services and click the **Next:Permissions** button
![Glue IAM](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/glue_role.png)

1. From the list of managed policies, attach the following by searching for their name and click **Next:Review** when done.

    + AWSGlueServiceRole
    + AWSGlueServiceNotebookRole
    + AmazonS3FullAccess

1. Give your role a name, such as **AWSGlueServiceRole** and click **Create Role**

![Glue Service Role](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/glue_role_final.png)

## Setup Amazon Redshift Spectrum service role
1. From the IAM console click **Roles** and create a new role

1. Select **Redshift** from the list of services followed by **Redshift - Customizable** use case and click the **Next:Permissions** button

![Redshift IAM](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/spectrum_role.png)

1. From the list of managed policies, attach the following by searching for their name and click **Next:Review** when done.
    + AWSGlueServiceRole
    + AmazonS3ReadOnlyAccess
    + AmazonAthenaFullAccess

![Spectrum roles](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/spectrum_role_review.png)

1. Give your role a name, such as **SpectrumServiceRole** and click Create Role

1. Once created, navigate back to the **Roles** section of IAM console and search for the role we just created. Select your role and copy the **Role ARN** to your clipboard

1. If you already have a Redshift cluster follow these [instructions](http://docs.aws.amazon.com/redshift/latest/dg/c-getting-started-using-spectrum-add-role.html) to attach the new role to it. If you do not have a cluster go ahead and create one making sure to associate this new role with the cluster at creation time.


## Setup Glue Data Catalog Connection to Redshift
1. Open the AWS Glue Console and select **Connections** from the left hand side under the Data Catalog section

Click **Add Connection** button

Give your connection a name and select **Amazon Redshift** from the connection type drop down. Click **Next**

![Glue connection](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/conn_name.png)

1. From the cluster drop down select your Redshift cluster. Enter your password, click **Next** and then **Finish**

After the connection is created, select your connection and click T**est Connection**. If the test fails, confirm the database name, username and password and try again.

Now that your connection is successful you can move on to the next section.
</p></details>



# Step 2: Crawling, Transforming and Querying <a name="rs-gsg-ctq"></a>

## References
1. [Apache Spark](https://spark.apache.org/)

1. [PySpark API Reference](http://spark.apache.org/docs/2.1.0/api/python/pyspark.sql.html)

## The Data Source
We will use a public data set provided by [Instacart in May 2017](https://tech.instacart.com/3-million-instacart-orders-open-sourced-d40d29ead6f2) to look at Instcart's customers' shopping pattern. You can find the data dictionary for the data set [here](https://gist.github.com/jeremystan/c3b39d947d9b88b3ccff3147dbcf6c6b)

## Crawling the data set
`IMPORTANT: If you are using AWS Glue in a region other than US-EAST-1, create a copy of S3 dataset into a local region. Without this, your Glue ETL Jobs will fail later in the Module`

### High-Level Instructions
Create a new Glue crawler that will catalog `s3://royon-customer-public/instacart/` and create a new database called `instacart` and `raw_` as the prefix. For Glue Crawler in a region outside US-EAST-1, create a copy of the S3 data into a local bucket first.

Example: (check that MYBUCKET is in the same region as Glue Crawler)
``` shell
aws s3 sync s3://royon-customer-public/ s3://MYBUCKET/
```

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

## Crawling the data set
1. Open the AWS Glue console

1. Select **Crawler** and click **Add Crawler**

1. Give your crawler a name and choose the Glue IAM role we created in Step 1 **AWSGlueServiceRole**

1. Select **S3** as the **Data Source** and specify a path in **another account**. Paste **s3://royon-customer-public/instacart/** as the S3 path.

1. Do not add any additional data sources and select **Run On Demand** for frequency

1. Create a new database called **instacart** and enter **raw_** as the table prefix

1. Click **Finish** to complete creating the crawler

1. Run the new crawler

</p></details>

## Transforming the data

### High-Level Instructions

See detailed instructions for transforming the data

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

## Transforming the Data
Before we get started with creating a Glue ETL job, we need to make one small change to the created tables. Select Tables in the Data Catalog and search for **raw_products**. Click **raw_products** to open up the table details. As you can see the Classification is `UNKNOWN`. We need to fix it before continuing.

1. Click **Edit Table**

1. Scroll down to **Classification** and change the value to **csv**

1. Click **Apply** to save the changes You may have also noticed that we don't have any column, that's ok, we'll fix this soon.

We'll now create a Glue ETL job that will process all 5 tables that the crawler created. We will create three fact tables in Redshift to hold the aisles, products and departments data. We will save the prior orders data in a Parquet optimized file format to Amazon S3.

`Before proceeding with Glue, connect to your Redshift cluster using a SQL client tool and create a new database called instacart`

EXAMPLE (within SQL workbench)

``` sql
CREATE DATABASE instacart
```

1. From the Glue ETL menu select **Jobs** and click **Add Job**

1. Give your job a name such as **instacart_etl** and select our service role. Leave all of the other options unchanged but provide a **Temporary Directory** for the output.

1. From the **Data Sources** list select one of the Instcart tables, lets say **raw_departments**. We can only select one table at this stage.

![Choosing Data Sources](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/rs_etl_select_source.png)


1. When choosing our data targets select **Create tables in your data target**. Select **JDBC** for data store. Enter the database name of your Redshift cluster and click **Next**

![Choosing Data Targets](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/rs_etl_data_target.png)


1. In this step we can change the source to target column mapping but we will not change this now. Click **Next**

1. Click **Finish** to complete creating our ETL

As you can see, AWS Glue created a script for you to get started. If you remember we still need to fix the Products table. Select everything in the script window and replace it with the code below.

`Make sure you update the values for GLUE_DB_CONNECTION and OUTPUT_S3_PATH to your own resources or the job will fail. GLUE_DB_CONNECTION is the Connection to Redshift you created in Step 1. `


``` python
import sys
from awsglue.transforms import *
from awsglue.utils import getResolvedOptions
from pyspark.context import SparkContext
from awsglue.context import GlueContext
from awsglue.dynamicframe import DynamicFrame
from awsglue.job import Job

##################
## Update these variables with your own information
##################
GLUE_DB_NAME = "instacart"
REDSHIFT_DB_NAME = "instacart"
GLUE_DB_CONNECTION = "YOUR_CATALOG_REDSHIFT_CONNECTION"

TBL_RAW_PRODUCTS = "raw_products"
TBL_RAW_ORDERS = "raw_orders"
TBL_RAW_ORDERS_PRIOR = "raw_orders_prior"
TBL_RAW_DEPT = "raw_departments"

OUTPUT_S3_PATH = "s3://YOURS3BUCKET/"
##################

## @params: [JOB_NAME]
args = getResolvedOptions(sys.argv, ['JOB_NAME'])

## Create Glue and Spark context variables
sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session
job = Job(glueContext)
job.init(args['JOB_NAME'], args)

## Define the source tables from where to read data
products = glueContext.create_dynamic_frame.from_catalog(database = GLUE_DB_NAME, table_name = TBL_RAW_PRODUCTS, transformation_ctx = "products").toDF()
orders_prior = glueContext.create_dynamic_frame.from_catalog(database = GLUE_DB_NAME, table_name = TBL_RAW_ORDERS_PRIOR, transformation_ctx = "orders_prior").toDF()
departments = glueContext.create_dynamic_frame.from_catalog(database = GLUE_DB_NAME, table_name = TBL_RAW_DEPT, transformation_ctx = "departments").toDF()

## Fix the products table which was missing columns and types
p_df = products.withColumn('product_id', products.col0.cast('bigint')) \
.withColumn('product_name', products.col1.cast('string')) \
.withColumn('aisle_id', products.col2.cast('bigint')) \
.withColumn('department_id', products.col2.cast('bigint')) \
.na.drop('any')
prods = p_df.select('product_id', 'product_name', 'aisle_id', 'department_id')

## Drop records that contain any null values
prods = prods.na.drop('any')
orders = orders_prior.na.drop('any')
departments = departments.na.drop('any')

## Convert from Spark DataFrame back to Glue DynamicFrame
df_prods = DynamicFrame.fromDF(prods, glueContext, 'df_prods')
df_depts = DynamicFrame.fromDF(departments, glueContext, 'df_depts')

## Write departments table to Redshift
datasink1 = glueContext.write_dynamic_frame.from_jdbc_conf(frame = df_depts, catalog_connection = GLUE_DB_CONNECTION, connection_options = {"dbtable": "departments", "database": REDSHIFT_DB_NAME}, redshift_tmp_dir = args["TempDir"], transformation_ctx = "datasink1")
## Write products table to Redshift
datasink2 = glueContext.write_dynamic_frame.from_jdbc_conf(frame = df_prods, catalog_connection = GLUE_DB_CONNECTION, connection_options = {"dbtable": "products", "database": REDSHIFT_DB_NAME}, redshift_tmp_dir = args["TempDir"], transformation_ctx = "datasink2")

## Write orders data to S3 in Parquet format
orders.coalesce(10) \
    .write \
    .mode('overwrite') \
    .parquet(OUTPUT_S3_PATH + 'orders/')

job.commit()
```

Now save and run your ETL job. One thing to note is that we're writing out a new set of data to our own S3 bucket. We need to configure a crawler to scan this new bucket and create appropriate tables in the Data Catalog so we can query them with Athena.

1. Before we go on, there is a little bit of cleanup we need to do in order to help the crawler produce better results. When Apache Spark (used by Glue ETL) does its work, it generates some temporary files. We will need to delete them. In the output S3 bucket delete files named **name_$folder$**, **_metadata** and **_common_metadata**

1. From the Glue console select **Crawlers** and create a new crawler. Point it to the top S3 bucket where you saved your results defined by **OUTPUT_S3_PATH** in the script above. Make sure you configure the table prefix to something other than raw_ so you can differentiate them from the previous ones we created such as **table_**.

1. When the ETL job completes we can run our new crawler. After the crawler completes you should see your new orders table. Also look in Redshift to verify that products and department tables were created and have data.

![Updated Data Catalog](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/instacart_orders_table.png)

</p></details>

## Using Redshift Spectrum

### High-Level Instructions

On your SQL Client tool, create a new external schema using the Glue data catalog database that uses the IAM role you created in Step 1. Run sample queries to test that Spectrum is able to pull data out from S3.

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>


### SQL Client
To run queries on Redshift you will need a SQL tool such as SQL Workbench/J. You can find instructions to set it up [here](http://docs.aws.amazon.com/redshift/latest/mgmt/connecting-using-workbench.html)

1. Before we can query data in S3 using Spectrum we need to create an external schema configured to interface with the Glue Data Catalog. Open up SQL Workbench/J or a similar tool and run the following commands in sequence:

    ``` sql
    SET autocommit ON
    ```

    ``` sql
    CREATE EXTERNAL SCHEMA instacart_schema 
    FROM data catalog 
    DATABASE 'instacart' 
    IAM_ROLE 'YOUR-SPECTRUM-ROLE-ARN'
    CREATE EXTERNAL DATABASE IF NOT EXISTS;
    ```

    In the above statement we create an external schema within Redshift to tell it that database `instacart` and all its tables are managed by the Glue Data Catalog. Also make sure to use the IAM role ARN you created in the first section of this Module.

    Now we have an external Redshift schema defined pointing to our database in Glue Data Catalog we can start running some queries.

1. Still from within SQL Workbench/J, lets verify that our fact tables (products and departments) where created in Redshift
    ``` sql
    SELECT * FROM products
    LIMIT 10;
    ```

    ![SQL product result](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/rs_sql_products.png)

1. Similarly for departments

    ``` sql
    SELECT * FROM departments
    LIMIT 10;
    ```

1. Now lets look at our orders in S3 using Redshift Spectrum. Make sure the replace the `Database Name`, `Schema Name` and the `Table Name` with your own values.

    ``` sql
    SELECT * FROM instacart.instacart_schema.table_orders
    LIMIT 20;
    ```

1. As you can see the ETL job succeeded and all of our data is loaded. Lets explore the data further.

1. Show the top 10 products most ordered by customers

    ``` sql
    SELECT products.product_name, COUNT(DISTINCT orders.order_id) order_count
    FROM taxis.instacart_schema.table_orders orders
    JOIN products ON orders.product_id = products.product_id
    GROUP BY products.product_name
    ORDER BY order_count DESC
    LIMIT 10
    ```

    ![SQL result top 10 products](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/rs_groupbyproducts.png)

</p></details>