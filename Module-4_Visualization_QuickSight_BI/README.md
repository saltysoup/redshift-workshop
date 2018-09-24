# Module 4 - Data Visualization

## Background
In this module, you will learn how to use Amazon QuickSight to visualize the instacart dataset by adding Amazon Athena as the analysis source. In order to optimize our visualization analysis, we will be using an Apache Parquet converted copy of the data.

## The Data Source
If you would like to transform the original CSV data (in Module 3) to an Apache Parquet format by yourself, feel free to go ahead. Otherwise, make a copy of the transformed format into your bucket from `s3://injae-redshift-workshop/instacart/`. The different CSV tables has been joined into 2 parquet format tables called `prior_orders` and `current_orders`.


``` shell
aws s3 sync s3://injae-redshift-workshop/instacart/ s3://YOURBUCKET/instacart/
```

Use the Glue Crawler to add the newly added `prior_orders` and `current_orders` to the Data Catalog. We will be using this via Athena to power Amazon QuickSight.

## Implementation Instructions

Each of the following sections provides an implementation overview and detailed, step-by-step instructions. The overview should provide enough context for you to complete the implementation if you're already familiar with the AWS Management Console or you want to explore the services yourself without following a walkthrough.

## References
1. [Managing Amazon QuickSight Permissions](http://docs.aws.amazon.com/quicksight/latest/user/managing-permissions.html)

1. [Creating Datasets using Amazon Athena](http://docs.aws.amazon.com/quicksight/latest/user/create-a-data-set-athena.html)

# Step 1: Setup IAM Permissions for QuickSight <a name="rs-gsg-quicksight-step1"></a>


### High-Level Instructions
Use or create a new IAM user that has the following IAM permissions:
+ All QuickSight API actions and Resources
+ IAMFullAccess
+ AWSQuicksightAthenaAccess


<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

Amazon QuickSight manages its own set of users and therefore you need to have an administrator account that is allowed to create new users. We'll use your current user as the administrator and therefore we'll need to add additional permissions for it to do its job.

QuickSight does not have a managed policy so we'll need to create one and then associate it with our user.

1. Open the IAM console

1. From the left hand side menu select **Policies** and click the Create Policy button

1. Select **Create Your Own Policy**

1. Name your policy **QuicksightPolicy** and enter the following into the **Policy Document** text box

    ``` json
    {
        "Statement": [
            {
                "Action": [
                    "quicksight:*"
                ],
                "Effect": "Allow",
                "Resource": "*"
            }
        ],
        "Version": "2012-10-17"
    }
    ```
1. Once created, select your username again from the **Users** section and add permissions as you've done previously. Search for **QuicksightPolicy** and add it.

1. Also add the following managed policies to your user

    + IAMFullAccess
    + AWSQuicksightAthenaAccess

</p></details>

# Step 2: Connecting with Amazon QuickSight <a name="rs-gsg-quicksight-step2"></a>
To explore the Instacart data we previously created we need to login to Amazon QuickSight and create a data set directly from Amazon Athena.

## Data Dictionary
[Please see Instacart data dictionary for reference](https://gist.github.com/jeremystan/c3b39d947d9b88b3ccff3147dbcf6c6b)

### High-Level Instructions
Explore the dataset through visualization by adding the Athena tables `prior_orders` and `current_orders` in a new Amazon QuickSight analysis. To import the data, you may need to [allow access to your S3 bucket](https://docs.aws.amazon.com/quicksight/latest/user/troubleshoot-connect-S3.html). To speed up building analysis, use SPICE to import the data into QuickSight first.

<details>
<summary><strong>Step-by-step instructions (expand for details)</strong></summary><p>

1. Open the QuickSight console

1. If it's your first time you'll be asked to enter an email address to create an account. If you've accessed QuickSight before you'll land in the main window

![Quicksight Dashboard](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/quicksight_main.png)

1. Next we'll need modify QuickSight's permissions to be able to access Athena and S3. From the top right corner, click on your user icon and select **Manage QuickSight** From the left hand side menu select **Account Settings** and click the **Edit AWS Permissions** button under Account Permissions.

1. On the permissions screen, tick the box for **Amazon Athena** and **Amazon S3**. When asked to select S3 buckets, select the bucket containing your instacart data. Click **Apply** and go back to the main screen.

1. Click the **New Analysis** button at the top left of the screen

1. Click **New Data Set** and select **Athena** from the list of sources

![Athena Source](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/quicksight_athena_source.png)

1. Give your data source a name and click **Create data source**

1. Select the **Instacart** database we created in Module 3 (or earlier in this Module)

1. Select the `table_prior_orders` as a starting point for us to explore

![table orders](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/quicksight_select_table.png)

1. Select **SPICE** and click Visualize

At this point you are presented with a blank graph so feel free to play around and build interesting visualizations.

For example, here is a graph of the count of customer orders that include items from each department

![example graph](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/quicksight_graph.png)

Here is another example of the top purchased products by the total number of customer orders
![example graph2](http://amazonathenahandson.s3-website-us-east-1.amazonaws.com/images/quicksight_graph2.png)

</p></details>


