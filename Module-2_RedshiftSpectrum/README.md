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