+++
author = "Kyriakos Antoniadis"
bg_image = "/images/banner/banner-1.jpg"
categories = ["Lake Formation", "Data Mesh", "SageMaker"]
date = 2022-11-15T23:00:00Z
description = ""
image = "/images/DM.jpg"
tags = []
title = "MLOps on Data Mesh"
type = "post"

+++
# Data Mesh

## Overview:

This model has been described by the founder of the data mesh pattern Zhamak Dehghani of Thoughtworks, who coined the term, [**Data Mesh**](https://martinfowler.com/articles/data-monolith-to-mesh.html).

_The code for this example is available on_ [_GitHub_](https://github.com/aws-samples/amazon-sagemaker-lakeformation-datamesh)_._

_Excerpt from_ [_AWS Machine Learning Blog_](https://aws.amazon.com/blogs/machine-learning/part-1-build-and-train-ml-models-using-a-data-mesh-architecture-on-aws/)

In her book [Data Mesh Delivering Data-Driven Value at Scale](https://www.oreilly.com/library/view/data-mesh/9781492092384/) defined four principles towards the objective of the data mesh:

* **Distributed domain ownership** – To pursue an organizational shift from centralized ownership of data by specialists who run the data platform technologies to a decentralized data ownership model, pushing ownership and accountability of the data back to the LoBs where data is produced (source-aligned domains) or consumed (consumption-aligned domains).
* **Data as a product** – To push upstream the accountability of sharing curated, high-quality, interoperable, and secure data assets. Therefore, data producers from different LoBs are responsible for making data in a consumable form right at the source.
* **Self-service analytics** – To streamline the experience of data users of analytics and ML so they can discover, access, and use data products with their preferred tools. Additionally, to streamline the experience of LoB data providers to build, deploy, and maintain data products via recipes and reusable components and templates.
* **Federated computational governance** – To federate and automate the decision-making involved in managing and controlling data access to be on the level of data owners from the different LoBs, which is still in line with the wider organization’s legal, compliance, and security policies that are ultimately enforced through the mesh.

## Use Case: Financial services

Typically, large financial services organizations have multiple LoBs, such as consumer banking, investment banking, and asset management, as also one or more analytics and ML CoE teams. Each LoB provides different services:

* The consumer banking LoB provides a variety of services to consumers and businesses, including credit and mortgage, cash management, payment solutions, deposit and investment products, and more
* The commercial or investment banking LoB offers comprehensive financial solutions, such as lending, bankruptcy risk, and wholesale payments to clients, including small businesses, mid-sized companies, and large corporations
* The asset management LoB provides retirement products and investment services across all asset classes

Each LoB defines their own data products, which are curated by people who understand the data and are best suited to specify who is authorized to use it, and how it can be used. In contrast, other LoBs and application domains such as the analytics and ML CoE are interested in discovering and consuming qualified data products, blending it together to generate insights, and making data-driven decisions.

The following illustration depicts some LoBs and examples of data products that they can share. It also shows the consumers of data products such as the analytics and ML CoE, who build ML models that can be deployed to customer-facing applications to further enhance the end-customer’s experience.

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image001.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image001.png)

Following the data mesh socio-technical concept, we start with the social aspect with a set of organizational steps, such as the following:

* Utilizing domain experts to define boundaries for each domain, so each data product can be mapped to a specific domain
* Identifying owners for data products provided from each domain, so each data product has a strategy defined by their owner
* Identifying governance policies from global and local or federated incentives, so when data consumers access a specific data product, the access policy associated with the product can be automatically enforced through a central data governance layer

### Data consumer: Analytics and ML CoE

The data consumers such as data scientists from the analytics and ML CoE need to be able to do the following:

* Discover and access relevant datasets for a given use case
* Be confident that datasets they want to access are already curated, up to date, and have robust descriptions
* Request access to datasets of interest to their business cases
* Use their preferred tools to query and process such datasets within their environment for ML without the need for replicating data from the original remote location or for worrying about engineering or infrastructure complexities associated with processing data physically stored in a remote site
* Get notified of any data updates made by the data owners

### Data producer: Domain ownership

The data producers, such as domain teams from different LoBs in the financial services org, need to register and share curated datasets that contain the following:

* Technical and operational metadata, such as database and table names and sizes, column schemas, and keys
* Business metadata such as data description, classification, and sensitivity
* Tracking metadata such as schema evolution from the source to the target form and any intermediate forms
* Data quality metadata such as correctness and completeness ratios and data bias
* Access policies and procedures

These are needed to allow data consumers to discover and access data without relying on manual procedures or having to contact the data product’s domain experts to gain more knowledge about the meaning of the data and how it can be accessed.

### Data governance: Discoverability, accessibility, and auditability

Organizations need to balance the agilities illustrated earlier with proper mitigation of the risks associated with data leaks. Particularly in regulated industries like financial services, there is a need to maintain central data governance to provide overall data access and audit control while reducing the storage footprint by avoiding multiple copies of the same data across different locations.

In traditional centralized data lake architectures, the data producers often publish raw data and pass on the responsibility of data curation, data quality management, and access control to data and infrastructure engineers in a centralized data platform team. However, these data platform teams may be less familiar with the various data domains, and still rely on support from the data producers to be able to properly curate and govern access to data according to the policies enforced at each data domain. In contrast, the data producers themselves are best positioned to provide curated, qualified data assets and are aware of the domain-specific access polices that need to be enforced while accessing data assets.

## Solution Overview:

The following diagram shows the high-level architecture of the proposed solution.

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/08/10/Picture-6.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/08/10/Picture-6.png)

We address data consumption by the analytics and ML CoE with [Amazon Athena](https://aws.amazon.com/athena/) and [Amazon SageMaker](https://aws.amazon.com/sagemaker/) in [part 2](https://aws.amazon.com/blogs/machine-learning/part-2-build-and-train-ml-models-using-a-data-mesh-architecture-on-aws/) of this series.

In this post, we focus on the data onboarding process into the data mesh and describe how an individual LoB such as the consumer banking domain data team can use AWS tools such as [AWS Glue](https://aws.amazon.com/glue/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc) and [AWS Glue DataBrew](https://aws.amazon.com/glue/features/databrew/) to prepare, curate, and enhance the quality of their data products, and then register those data products into the central data governance account through [AWS Lake Formation](https://aws.amazon.com/lake-formation/).

### Consumer banking LoB (data producer)

One of the core principles of data mesh is the concept of data as a product. It’s very important that the consumer banking domain data teamwork on preparing data products that are ready for use by data consumers. This can be done by using AWS extract, transform, and load (ETL) tools like AWS Glue to process raw data collected on [Amazon Simple Storage Service](http://aws.amazon.com/s3) (Amazon S3), or alternatively connect to the operational data stores where the data is produced. You can also use [DataBrew](https://aws.amazon.com/glue/features/databrew/), which is a no-code visual data preparation tool that makes it easy to clean and normalize data.

For example, while preparing the consumer credit profile data product, the consumer banking domain data team can make a simple curation to translate from German to English the attribute names of the raw data retrieved from the open-source dataset [Statlog German credit data](https://archive.ics.uci.edu/ml/datasets/South+German+Credit+(UPDATE)), which consists of 20 attributes and 1,000 rows.

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image005.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image005.png)

### Data governance

The core AWS service for enabling data mesh governance is Lake Formation. Lake Formation offers the ability to enforce data governance within each data domain and across domains to ensure data is easily discoverable and secure. It provides a federated security model that can be administered centrally, with best practices for data discovery, security, and compliance, while allowing high agility within each domain.

Lake Formation offers an API to simplify how data is ingested, stored, and managed, together with row-level security to protect your data. It also provides functionality like granular access control, governed tables, and storage optimization.

In addition, Lake Formations offers a [Data Sharing API ](https://docs.aws.amazon.com/lake-formation/latest/dg/sharing-catalog-resources.html)that you can use to share data [across different accounts](https://docs.aws.amazon.com/lake-formation/latest/dg/crosss-account-how-works.html). This allows the analytics and ML CoE consumer to run Athena queries that query and join tables across multiple accounts. For more information, refer to the [AWS Lake Formation Developer Guide](https://docs.aws.amazon.com/lake-formation/latest/dg/what-is-lake-formation.html).

[AWS Resource Access Manager](https://aws.amazon.com/ram/) (AWS RAM) provides a secure way to share resources via [AWS Identity and Access Manager](https://aws.amazon.com/iam/) (IAM) roles and users across AWS accounts within an organization or organizational units (OUs) in [AWS Organizations.](https://aws.amazon.com/organizations/)

Lake Formation together with AWS RAM provides one way to manage data sharing and access across AWS accounts. We refer to this approach as [RAM-based access control](https://docs.aws.amazon.com/lake-formation/latest/dg/crosss-account-how-works.html). For more details about this approach, refer to [**Build a data sharing workflow with AWS Lake Formation for your data mesh**](https://aws.amazon.com/blogs/big-data/build-a-data-sharing-workflow-with-aws-lake-formation-for-your-data-mesh/)**.**

Lake Formation also offers another way to manage data sharing and access using [Lake Formation tags](https://docs.aws.amazon.com/lake-formation/latest/dg/TBAC-creating-tags.html). We refer to this approach as [tag-based access control](https://docs.aws.amazon.com/lake-formation/latest/dg/tag-based-access-control.html). For more details, refer to [Build a modern data architecture and data mesh pattern at scale using AWS Lake Formation tag-based access control](https://aws.amazon.com/blogs/big-data/build-a-modern-data-architecture-and-data-mesh-pattern-at-scale-using-aws-lake-formation-tag-based-access-control/).

Throughout this post, we use the tag-based access control approach because it simplifies the creation of policies on a smaller number of logical tags that are commonly found in different LoBs instead of specifying policies on named resources at the infrastructure level.

## **Prerequisites: Set up resources with AWS CloudFormation**:

To set up a data mesh architecture, you need at least three AWS accounts: a producer account, a central account, and a consumer account.

Three [AWS CloudFormation](https://aws.amazon.com/cloudformation/) templates are provided in this post:

1. **producer account**
2. **central account**
3. **consumer account**

Deploy the CloudFormation templates in the order of producer, central, and consumer, because there are dependencies between the templates.

The CloudFormation template for the **producer account** generates the following resources:

* Two [Amazon Simple Storage Service](http://aws.amazon.com/s3) (Amazon S3) buckets:
  * credit-card, which holds one table:
    * Credit_Card
* Allow Amazon S3 bucket access for the central account Lake Formation service role.
* One AWS Glue crawler
* One AWS Glue crawler service role
* Grant permissions on the S3 bucket locations credit-card-lf-<ProducerAccountID>-<aws-region> to the AWS Glue crawler role
* One producer steward IAM user

The CloudFormation template for the **central account** generates the following resources:

* Two IAM users:
  * DataMeshOwner
  * ProducerSteward
* Grant DataMeshOwner as the LakeFormation Admin
* One IAM role:
  * LFRegisterLocationServiceRole
* Two IAM policies:
  * ProducerStewardPolicy
  * S3DataLakePolicy
* Create databases “credit-card” for ProducerSteward to manage Data Catalog
* Share the data location permission for producer account to manage Data Catalog

The CloudFormation template for the **consumer account** generates the following resources:

* One S3 bucket:
  * <AWS Account ID>-<aws-region>-athena-logs
* One Athena workgroup:
  * consumer-workgroup
* Three IAM users:
  * ConsumerAdmin

### **Launch the CloudFormation stack in the central account**

To create resources in the central account, complete the following steps:

 1. Sign in to the central account’s AWS CloudFormation console in the target Region.
 2. Choose **Launch Stack:**   
    [![Launch Stack](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.001.jpeg)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=stack-central&templateURL=https://aws-bigdata-blog.s3.amazonaws.com/artifacts/lakeformationtbac/cfn/tbac-cross-account-central.yaml)
 3. Choose **Next**.
 4. For **Stack name**, enter _stack-central_.
 5. For **DataMeshOwnerUserPassword**, enter the password you want for the data lake admin IAM user in the central account.
 6. For **ProducerStewardUserPassword**, enter the password you want for the producer steward IAM user in the producer account.
 7. For **ProducerAWSAccount**, enter the AWS <ProducerAccountID>.
 8. Choose **Next**.
 9. On the next page, choose **Next**.
10. Review the details on the final page and select **I acknowledge that AWS CloudFormation might create IAM resources**.
11. Choose **Create** **stack**.
12. Collect the value for LFRegisterLocationServiceRole on the stack’s Outputs tab.

### **Launch the CloudFormation stack in the producer account**

To set up resources in the producer account, complete the following steps:

 1. Sign in to the producer account’s AWS CloudFormation console in the target Region.
 2. Choose **Launch Stack**:[
    ![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.002.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=stack-producer&templateURL=https://aws-bigdata-blog.s3.amazonaws.com/artifacts/lakeformationtbac/cfn/tbac-cross-account-producer.yaml)
 3. Choose **Next**.
 4. For **Stack name**, enter stack-producer.
 5. For **CentralAccountID**, copy and paste the value of the <CentralAccountID> .
 6. For **CentralAccountLFServiceRole**, copy and paste the value of the LFRegisterLocationServiceRole collected from the stack-central.
 7. For **LFDatabaseName**, keep the default value of the lf-ml database name.
 8. For **ProducerStewardUserPassword**, enter the password you want for the data lake admin IAM user on the producer account.
 9. Choose **Next**.
10. On the next page, choose **Next**.
11. Review the details on the final page and select **I acknowledge that AWS CloudFormation might create IAM resources**.
12. Choose **Create stack.**

### **Launch the CloudFormation stack in the consumer account**

To create resources in the consumer account, complete the following steps:

 1. Sign in to the consumer account’s AWS CloudFormation console in the target Region.
 2. Choose **Launch Stack**:
    [![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.002.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=stack-consumer&templateURL=https://aws-bigdata-blog.s3.amazonaws.com/artifacts/lakeformationtbac/cfn/tbac-cross-account-consumer.yaml)
 3. Choose **Next**.
 4. For **Stack name**, enter stack-consumer.
 5. For **ConsumerAdminUserName** and **ConsumerAdminUserPassword**, enter the user name and password you want for the data lake admin IAM user.
 6. For **ConsumerAnalyst1UserName** and **ConsumerAnalyst1UserPassword**, enter the user name and password you want for the consumeranalyst1 IAM user.
 7. For **ConsumerAnalyst2UserName** and **ConsumerAnalyst2UserPassword**, enter the user name and password you want for the consumeranalyst2 IAM user.
 8. Choose **Next**.
 9. On the next page, choose **Next**.
10. Review the details on the final page and select **I acknowledge that AWS CloudFormation might create IAM resources**.
11. Choose **Create stack**.

## **Configure Lake Formation cross-account sharing**:

After you create your resources with AWS CloudFormation, you perform the following steps in the producer and central account to set up Lake Formation cross-account sharing.

### **Central governance account**

In the central account, complete the following steps:

1. Sign in to the Lake Formation console as admin.
2. In the navigation pane, choose **Permissions**, then choose **Administrative roles and tasks**.

The CloudFormation template added the data mesh owner as the data lake administrator.

![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.003.png)

Next, we update the Data Catalog settings to use Lake Formation permissions to control catalog resources instead of IAM-based access control.

1. In the navigation pane, under **Data catalog**¸ choose **Settings**.
2. Uncheck **Use only IAM access control for new databases**.
3. Uncheck **Use only IAM access control for new tables in new databases**.
4. Choose **Save**.
   ![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.004.png)

Next, we need to set up the AWS Glue Data Catalog resource policy to grant cross-account access to Data Catalog resources.

Use the following policy, and replace the account number and Region with your own values:

`{`

`"PolicyInJson": "{\"Version\" : \"2012-10-17\",\"Statement\" : [ {\"Effect\" : \"Allow\",\"Principal\" : {\"AWS\" : [\"arn:aws:iam::<ProducerAccountID>:root\",\"arn:aws:iam::<ConsumerAccountID>:root\"]},\"Action\" : \"glue:\*\",\"Resource\" : [ \"arn:aws:glue:<aws-region>:<CentralAccountID>:table/\*\", \"arn:aws:glue:<aws-region>:<CentralAccountID>:database/\*\", \"arn:aws:glue:<aws-region>:<CentralAccountID>:catalog\" ],\"Condition\" : {\"Bool\" : {\"glue:EvaluatedByLakeFormationTags\" : \"true\"}}}, {\"Effect\" : \"Allow\",\"Principal\" : {\"Service\" : \"ram.amazonaws.com\"},\"Action\" : \"glue:ShareResource\",\"Resource\" : [ \"arn:aws:glue:<aws-region>:<CentralAccountID>:table/\*\", \"arn:aws:glue:<aws-region>:<CentralAccountID>:database/\*\", \"arn:aws:glue:<aws-region>:<CentralAccountID>:catalog\" ]} ]}",`

`"EnableHybrid": "TRUE"`

`}`

Replace the <aws-region>, <ProducerAccountID>, <ConsumerAccountID> and <CentralAccountID> values in the above policy as appropriate and save it in a file called policy.json.

1. Next, run the following [AWS Command Line Interface](http://aws.amazon.com/cli) (AWS CLI) command on [AWS CloudShell](https://aws.amazon.com/cloudshell/).

aws glue put-resource-policy --region <aws-region> --cli-input-json file://policy.json

For more information about this policy, see [put-resource-policy](https://docs.aws.amazon.com/cli/latest/reference/glue/put-resource-policy.html).

1. Next, we verify the source data S3 bucket isregistered as data lake location in the central account. This is completed by the CloudFormation template.
2. Under **Register and ingest** in the navigation pane, choose **Data lake locations.**

You should see the S3 bucket registered under the data lake locations.

![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.005.jpeg)

## **Configure Lake Formation Data Catalog settings in the central account**:

After we complete all the prerequisites, we start the data mesh configuration. We log in as DataMeshOwner in the central account.

### **Define LF-tags**

DataMeshOwner creates the tag ontology by defining LF-tags. Complete the following steps:

1. On the Lake Formation console, under **Permissions** in the navigation pane, under **Administrative roles and tasks**, choose **LF-Tags.**
2. Choose **Add LF-tags**.
3. For **Key**, enter database and for **Values**, choose credit-card.
4. Choose **Add** and then **Add LF-tag**.

![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.006.png)

1. The result looks like the image below.

![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.007.png)

### **Grant permissions**

We grant ProducerSteward in the central accounts [describe and associate permissions](https://docs.aws.amazon.com/lake-formation/latest/dg/TBAC-security.html) on the preceding tag ontology. This enables ProducerSteward to view the LF-tags and assign them to Data Catalog resources (databases, tables, and columns). ProducerSteward in the central account can further grant the permission to ProducerSteward in the producer account. For more information, see [Granting, Revoking, and Listing LF-Tag Permissions.](https://docs.aws.amazon.com/lake-formation/latest/dg/TBAC-granting-tags.html) When you have multiple producers, grant the relevant tags to each steward.

In our situation, we will only have one LF tag assigned which points to the database. This could be further improved by adding extra tags for more granularity on data access. But it is out of the scope for this guide.

1. Under **Permissions** in the navigation pane, under **Administrative roles and tasks**, choose **LF-tag permissions**.
2. Choose **Grant**.
3. For **IAM users and roles**, choose the ProducerSteward user.
4. In the **LF-Tags** section, add all three key-values:
   1. Key database with values credit-card.

![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.008.png)

1. For **Permissions**, select **Describe** and **Associate** for both **LF-tag permissions** and **Grantable permissions**.
2. Choose **Grant.
   ![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.009.png)**

Next, we grant ProducerSteward tag-based data lake permissions. This enables ProducerSteward to create, alter, and drop tables in the databases with corresponding tags. ProducerSteward in the producer account can further grant the permission across accounts.

1. In the navigation pane, under **Permissions**, **Data lake permissions**, choose **Grant**.
2. For **Principals**, choose **IAM users and roles**, and choose ProducerSteward.
3. For **LF-tags or catalog resources**, select **Resources matched by LF-Tags (recommended)**.
4. Choose **Add LF-Tag**.
5. For **Key**, choose database and for **Values**, choose credit-card.
6. For **Database permissions**, select the [Super permission](https://docs.aws.amazon.com/lake-formation/latest/dg/lf-permissions-reference.html#perm-Super) because ProducerSteward owns the producer databases.

This permission allows a principal to perform every supported Lake Formation operation on the database. Use this admin permission when a principal is trusted with all operations.

1. Select **Super** under **Grantable permissions** so the ProducerSteward user can grant database-level permissions to the producer and consumer accounts.
2. For **Table permissions**, select **Super**.
3. Select **Super** permission under **Grantable permissions**.
4. Choose **Grant**.

![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.010.png)

## **Producer data steward actions in the central account**:

Next, we log in as the ProducerSteward user in the central account and create skeleton databases.

1. Sign in to the Lake Formation console as ProducerSteward.
2. In the navigation pane, under **Data catalog,** select **Databases**.
3. Choose the credit-card database.
4. On the **Actions** menu, choose **Edit LF-tags**

![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.011.jpeg)

1. Choose **Assign new LF-tag**.
2. For **Assigned** **Keys**, enter the database and for **Values**, choose credit-card.
3. Choose **Save.**

This assigns the database=credit-card tag to the credit-card database.

![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.012.png)

Next, we share the LF-tags and data lake permissions with the producer account so that ProducerSteward in the producer account can run AWS Glue crawlers and generate tables in the preceding skeleton databases.

 1. Under **Permissions** in the navigation pane, under **Administrative roles and tasks**, choose **LF-tag permissions**.
 2. Choose **Grant**.
 3. For **Principals**, select **External accounts**.
 4. For **AWS account or AWS organization**, enter the account ID for the producer account.
 5. In the **LF-Tags** section, we only need to add database-level tags.
 6. For **Key**, enter database and for **Values**, choose credit-card.
 7. For **Permissions**, choose **Describe** and **Associate** for both **LF-tag permissions** and **Grantable permissions**.
 8. Choose **Grant**.
    ![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.013.jpeg)
 9. In the navigation pane, under **Permissions**, **Data lake permissions**, choose **Grant**.
10. For **Principals**, select **External accounts**.
11. For **AWS account or AWS organization**, enter the account ID for the producer account.
12. For **LF-tags or catalog resources**, select **Resources matched by LF-Tags (recommended)**.
13. Choose **Add LF-Tag**.
14. Choose the key database and value credit-card.
    ![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.014.jpeg)
15. For **Database permissions**, select **Create table** and **Describe** because the ProducerSteward user in the producer account will add tables in the database.
16. Select **Create table** and **Describe** under **Grantable permissions** so the ProducerSteward user can further grant the permission to the AWS Glue crawler.
17. For **Table permissions**, select all the permissions.
18. Select all the permissions under **Grantable permissions.**
19. Choose **Grant**.
    ![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.015.png)

Now the Lake Formation administrators on the producer account side has the right permissions to add tables.

### **Crawl source tables in the producer account**

Next, we log in as the ProducerSteward user in the producer account to crawl the source tables for the Cards and Retail databases.

1. Sign in to the Lake Formation console as ProducerSteward.
2. In the navigation pane, under **Administrative Roles and Tasks**, verify that ProducerSteward is configured as the data lake administrator.

![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.016.jpeg)

1. In the navigation pane, under **Permissions**, then choose **Administrative roles and tasks**, choose **LF-Tags**.

You can verify the database tag that was shared with the producer account.
![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.017.jpeg)

1. In the navigation pane, under **Data catalog**, select **Databases.**

You can verify the two databases cards and retail that were shared with the producer account from the previous step.

![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.018.jpeg)

Now, we create a [resource link](https://docs.aws.amazon.com/lake-formation/latest/dg/resource-links-about.html) in the producer account for this database. This link point at the shared database and is used by AWS Glue crawler to create the tables. First, we create a resource link for the credit-card database.

1. Select the cards database and on the **Actions** menu, choose **Create resource link**.
   ![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.019.jpeg)
2. For **Resource link name**, enter rl_credit-card.
3. Choose **Create**.

After the resource link creation, you should see both the resource link databases as shown in the following screenshot.
![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.020.jpeg)

Next, we need to grant permissions to the AWS Glue crawler role so that the crawler can crawl the source bucket and create the tables.

 1. Select the rl_credit-card database and on the **Actions** menu, choose **Grant**.
 2. In the **Grant data permissions** section, select **IAM users and roles**, and choose the AWS Glue crawler role that was created by the CloudFormation template (for example, stack-producer-AWSGlueServiceRoleDefault-xxxxxx).
 3. For **Databases**, choose rl_credit-card.
 4. For **Resource link permissions**, select **Describe**.
 5. Choose **Grant**.
    ![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.021.png)
 6. Next, in the navigation pane, choose **Data lake Permissions** and choose **Grant**.
 7. For **IAM users and roles**, choose the role stack-producer-AWSGlueServiceRoleDefault-XXXX.
 8. For **LF-Tags or catalog resources**, select **Resources matched by LF-Tags**.
 9. Enter the key database and values credit-card.
10. For **Database permissions**, select **Create table** and **Describe**.
11. For **Table permissions**, choose **Select**, **Describe**, and **Alter**.
12. Choose **Grant**.

Next, we will verify grant permissions on the S3 bucket locations corresponding to credit-card producer to the AWS Glue crawler role. This is completed by the CloudFormation template.

In the navigation pane, under **Permissions**, on the **Data Locations**, you should see the locations.
![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.022.jpeg)

Now we’re ready to run the crawler. We configure the crawler that the CloudFormation template created, to point it to the resource link database.

1. On the AWS Glue console, under **Data catalog** in the navigation pane, choose **Crawlers**.

The crawler you created should be listed.

1. Select the crawler for the cards database CardsCrawler-xxxxxxxxxxxx and on the **Action** menu, choose **Edit crawler**.
   ![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.023.jpeg)
2. For the input data store, choose the S3 bucket for the credit-card producer.
3. For **IAM role**, choose the AWS Glue service role created by the CloudFormation template.
4. For **Schedule**, choose **Run on demand**.
5. For the output database, choose the resource link database rl_credit-card corresponding to the cards database.
   ![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.024.jpeg)
6. Verify all the information and choose **Save**.
7. Select the crawler and choose **Run crawler**.

When the crawler finish, it creates table(s) corresponding to the producer in its respective resource link database. The table schemas are present in the shared database in the central account.

### **Configure Lake Formation tags in the central account**

1. Log in to central account as IAM user ProducerSteward.
2. On the Lake Formation console, in the navigation pane, choose **Data catalog** and then choose **Tables.**

You should see the credit_card table corresponding to credit-card database.
![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.025.jpeg)

### **Grant tag permissions**

Next, grant LF-tag permissions to the external consumer account.

1. On the Lake Formation console, in the navigation pane, choose **Permissions**, then choose **Administrative roles and tasks** and choose **LF-tag permissions**.
2. Choose **Grant**.
3. For **Principals**, select **External accounts**.
4. For **AWS account or AWS organization**, enter the AWS account number corresponding to the consumer account.
5. For **LF-Tags**, choose **Add LF-Tag**.
6. For **Key**, choose database and for **Values**, choose credit-card.
   ![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.026.jpeg)
7. For **Permissions**, choose **Describe**.
8. For **Grantable permissions**, choose **Describe**.
9. Choose **Grant**.
   ![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.027.png)

Next, we grant Lake Formation policy tag expression permissions to the external consumer account.

1. In the navigation pane, choose **Data lake permissions** and choose **Grant**.
2. In the **Principals** section, select **External accounts**.
3. For **AWS account or AWS organization**, enter the AWS account number corresponding to the consumer account.
4. For **LF-Tags or catalog resources**, select **Resources matched by LF-Tags**.
5. Choose **Add LF-Tag**.
6. For **Key**, choose database and for **Values**¸ choose credit-card.
7. For **Database permissions**, select **Describe**.
8. For **Grantable permissions**, select **Describe**.
9. Choose **Grant**.

Next, we grant table permissions.

1. In the navigation pane, choose **Data lake permissions** and choose **Grant**.
2. For **Principals**, select **External accounts**.
3. For **AWS account or AWS organization**, enter the AWS account number corresponding to the consumer account.
4. For **LF-Tags or catalog resources**, select **Resources matched by LF-Tags**.
5. Add key database with value credit-card
6. For **Table Permissions**, select **Select** and **Describe**.
7. For **Grantable permissions**, select **Select** and **Describe**.
8. Choose **Grant**.

## **Share and consume tables in the consumer account**:

When you sign in to the Lake Formation console in the consumer account as ConsumerAdmin, you can see the tags and the corresponding value that was shared by the producer.

![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.028.jpeg)

In these next steps, we share and consume the table in the consumer account.

### **Create a resource link to the shared database**

On the **Databases** page on the Lake Formation console, you can see all the databases that were shared to the consumer account. To create a resource link, complete the following steps:

1. On the **Databases** page, select the credit-card database and on the **Actions** menu, choose **Create resource link**.
   ![](/images/Aspose.Words.06580717-e26c-40ea-bebf-be8283065088.029.jpeg)
2. Enter the resource link name as rl_credit-card.
3. Leave the shared database and shared database’s owner ID as default.
4. Choose **Create**.

### **Grant Describe permission to SageMaker (SM) role used by the SageMaker Studio user**

To grant Describe permissions on resource link databases to SM Studio user, complete the following steps:

1. On the **Databases** page, select the resource database rl_credit-card and on the **Actions** menu, choose **Grant**.
2. In the **Grant data permissions** section, select **IAM users and roles**.
3. Choose the role corresponding to the SageMaker Studio user.
4. In the **Resource link permissions** section, select **Describe**.
5. Choose **Grant**.
   Grant Tag permissions to ConsumerAnalyst1

To grant Tag permissions on the database:credit-card tag to SM Studio user to access the credit_card table, complete the following steps:

1. On the Lake Formation console, on the **Data permission** page, choose **Grant**.
2. In the **Grant data permissions** section, select **IAM users and roles**.
3. Choose the role corresponding to the SageMaker Studio user
4. For **LF-Tags or catalog resources**, select **Resources matched by LF-Tags**.
5. Add the key database with value credit-card
6. For **Table permissions**, select **Select** and **Describe**.
7. Choose **Grant**.

The last step for the SageMaker Studio user to be able to access the data is to update the Studio user role by adding the following policy to the SageMaker role used by the Studio user

{

"Version": "2012-10-17",

"Statement": \[

{

"Sid": "VisualEditor0",

"Effect": "Allow",

"Action": \[

"lakeformation:GetDataAccess",

"glue:GetDatabase"

\],

"Resource": "*"

}

\]

}

## Data product registration

The following architecture describes the detailed steps of how the consumer banking LoB team acting as data producers can register their data products in the central data governance account (onboard data products to the organization data mesh).

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image010.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image010.png)

The general steps to register a data product are as follows:

1. Create a target database for the data product in the central governance account. As an example, the CloudFormation template from the central account already creates the target database `credit-card`.
2. Share the created target database with the origin in the producer account.
3. Create a resource link of the shared database in the producer account. In the following screenshot, we see on the Lake Formation console in the producer account that `rl_credit-card` is the resource link of the `credit-card` database.
   [![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image012.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image012.png)
4. Populate tables (with the data curated in the producer account) inside the resource link database (`rl_credit-card`) using an AWS Glue crawler in the producer account.
   [![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image014.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image014.png)

The created table automatically appears in the central governance account. The following screenshot shows an example of the table in Lake Formation in the central account. This is after performing the earlier steps to populate the resource link database `rl_credit-card` in the producer account.

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image016.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image016.png)

This is the second part of a series that showcases the machine learning (ML) lifecycle with a data mesh design pattern for a large enterprise with multiple lines of business (LOBs) and a Center of Excellence (CoE) for analytics and ML.

In this post, we address the analytics and ML platform team as a consumer in the data mesh. The platform team sets up the ML environment for the data scientists and helps them get access to the necessary data products in the data mesh. The data scientists in this team use [Amazon SageMaker](https://aws.amazon.com/sagemaker/) to build and train a credit risk prediction model using the shared credit risk data product from the consumer banking LoB.

## Analytics and ML consumer in a data mesh architecture

Let’s recap the high-level architecture that highlights the key components in the data mesh architecture.

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/08/01/image-68.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/08/01/image-68.png)

In the data producer block 1 (left), there is a data processing stage to ensure that shared data is well-qualified and curated. The central data governance block 2 (center) acts as a centralized data catalog with metadata of various registered data products. The data consumer block 3 (right) requests access to datasets from the central catalog and queries and processes the data to build and train ML models.

With SageMaker, data scientists and developers in the ML CoE can quickly and easily build and train ML models, and then directly deploy them into a production-ready hosted environment. SageMaker provides easy access to your data sources for exploration and analysis, and also provides common ML algorithms and frameworks that are optimized to run efficiently against extremely large data in a distributed environment. It’s easy to get started with [Amazon SageMaker Studio](https://docs.aws.amazon.com/sagemaker/latest/dg/studio.html), a web-based integrated development environment (IDE), by completing the SageMaker domain [onboarding process](https://docs.aws.amazon.com/sagemaker/latest/dg/gs-studio-onboard.html). For more information, refer to the [Amazon SageMaker Developer Guide](https://docs.aws.amazon.com/sagemaker/latest/dg/whatis.html).

## Data product consumption by the analytics and ML CoE

The following architecture diagram describes the steps required by the analytics and ML CoE consumer to get access to the registered data product in the central data catalog and process the data to build and train an ML model.

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image026.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image026.png)

The workflow consists of the following components:

1. The producer data steward provides access in the central account to the database and table to the consumer account. The database is now reflected as a shared database in the consumer account.
2. The consumer admin creates a resource link in the consumer account to the database shared by the central account. The following screenshot shows an example in the consumer account, with `rl_credit-card` being the resource link of the `credit-card` database.
   [![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image028.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image028.png)
   [![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image030.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image030.png)
3. The consumer admin provides the Studio [AWS Identity and Access Management](http://aws.amazon.com/iam) (IAM) execution role access to the resource linked database and the table identified in the Lake Formation tag. In the following example, the consumer admin provided to the SageMaker execution role has permission to access `rl_credit-card` and the table satisfying the Lake Formation tag expression.
   [![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image032.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image032.png)
4. Once assigned an execution role, data scientists in SageMaker can use

   Amazon Athena

   to query the table via the resource link database in Lake Formation.
   1. For data exploration, they can use Studio notebooks to process the data with interactive querying via Athena.
   2. For data processing and feature engineering, they can run SageMaker processing jobs with an Athena data source and output results back to [Amazon Simple Storage Service](http://aws.amazon.com/s3) (Amazon S3).
   3. After the data is processed and available in Amazon S3 on the ML CoE account, data scientists can use SageMaker training jobs to train models and [SageMaker Pipelines](https://aws.amazon.com/sagemaker/pipelines/) to automate model-building workflows.
   4. Data scientists can also use the SageMaker model registry to register the models.

### Data exploration

The following diagram illustrates the data exploration workflow in the data consumer account.

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image034.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image034.png)

The consumer starts by querying a sample of the data from the `credit_risk` table with Athena in a Studio notebook. When querying data via Athena, the intermediate results are also saved in Amazon S3. You can use the [AWS Data Wrangler library](https://aws-data-wrangler.readthedocs.io/en/stable/) to run a query on Athena in a Studio notebook for data exploration. The following code example shows [how to query Athena](https://aws-data-wrangler.readthedocs.io/en/stable/tutorials/006 - Amazon Athena.html) to fetch the results as a dataframe for data exploration:

```sql
df= wr.athena.read_sql_query('SELECT * FROM credit_card LIMIT 10;', database="rl_credit-card", ctas_approach=False)
```

Now that you have a subset of the data as a dataframe, you can start exploring the data and see what feature engineering updates are needed for model training. An example of data exploration is shown in the following screenshot.

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image036.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image036.png)

When you query the database, you can see the access logs from the Lake Formation console, as shown in the following screenshot. These logs give you information about who or which service has used Lake Formation, including the IAM role and time of access. The screenshot shows a log about SageMaker accessing the table `credit_risk` in AWS Glue via Athena. In the log, you can see the additional audit context that contains the query ID that matches the query ID in Athena.

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image038.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image038.png)

The following screenshot shows the Athena query run ID that matches the query ID from the preceding log. This shows the data accessed with the SQL query. You can see what data has been queried by navigating to the Athena console, choosing the **Recent queries** tab, and then looking for the run ID that matches the query ID from the additional audit context.

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image040.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image040.png)

### Data processing

After data exploration, you may want to preprocess the entire large dataset for feature engineering before training a model. The following diagram illustrates the data processing procedure.

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image042.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image042.png)

In this example, we use a SageMaker processing job, in which we define an Athena dataset definition. The processing job queries the data via Athena and uses a script to split the data into training, testing, and validation datasets. The results of the processing job are saved to Amazon S3. To learn how to configure a processing job with Athena, refer to [Use Amazon Athena in a processing job with Amazon SageMaker](https://medium.com/p/d43272d69a78).

In this example, you can use the Python SDK to trigger a processing job with the Scikit-learn framework. Before triggering, you can configure the [inputs parameter](https://sagemaker.readthedocs.io/en/stable/api/training/processing.html#sagemaker.processing.ProcessingInput) to get the input data via the Athena dataset definition, as shown in the following code. The dataset contains the location to download the results from Athena to the processing container and the configuration for the SQL query. When the processing job is finished, the results are saved in Amazon S3.

```code
AthenaDataset = AthenaDatasetDefinition (
  catalog = 'AwsDataCatalog', 
  database = 'rl_credit-card', 
  query_string = 'SELECT * FROM "rl_credit-card"."credit_card""',                                
  output_s3_uri = 's3://sagemaker-us-east-1-********7363/athenaqueries/', 
  work_group = 'primary', 
  output_format = 'PARQUET')

dataSet = DatasetDefinition(
  athena_dataset_definition = AthenaDataset, 
  local_path='/opt/ml/processing/input/dataset.parquet')


sklearn_processor.run(
    code="processing/preprocessor.py",
    inputs=[ProcessingInput(
      input_name="dataset", 
      destination="/opt/ml/processing/input", 
      dataset_definition=dataSet)],
    outputs=[
        ProcessingOutput(
            output_name="train_data", source="/opt/ml/processing/train", destination=train_data_path
        ),
        ProcessingOutput(
            output_name="val_data", source="/opt/ml/processing/val", destination=val_data_path
        ),
        ProcessingOutput(
            output_name="model", source="/opt/ml/processing/model", destination=model_path
        ),
        ProcessingOutput(
            output_name="test_data", source="/opt/ml/processing/test", destination=test_data_path
        ),
    ],
    arguments=["--train-test-split-ratio", "0.2"],
    logs=False,
)
```

### Model training and model registration

After preprocessing the data, you can train the model with the preprocessed data saved in Amazon S3. The following diagram illustrates the model training and registration process.

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image044.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image044.png)

For data exploration and SageMaker processing jobs, you can retrieve the data in the data mesh via Athena. Although the SageMaker Training API doesn’t include a parameter to configure an Athena data source, you can query data via Athena in the training script itself.

In this example, the preprocessed data is now available in Amazon S3 and can be used directly to train an XGBoost model with SageMaker Script Mode. You can provide the script, hyperparameters, instance type, and all the additional parameters needed to successfully train the model. You can trigger the SageMaker estimator with the training and validation data in Amazon S3. When the model training is complete, you can register the model in the SageMaker model registry for experiment tracking and deployment to a production account.

```python
estimator = XGBoost(
    entry_point=entry_point,
    source_dir=source_dir,
    output_path=output_path,
    code_location=code_location,
    hyperparameters=hyperparameters,
    instance_type="ml.c5.xlarge",
    instance_count=1,
    framework_version="0.90-2",
    py_version="py3",
    role=role,
)

inputs = {"train": train_input_data, "validation": val_input_data}

estimator.fit(inputs, job_name=job_name)
```

## Next steps

You can make incremental updates to the solution to address requirements around data updates and model retraining, automatic deletion of intermediate data in Amazon S3, and integrating a feature store. We discuss each of these in more detail in the following sections.

### Data updates and model retraining triggers

The following diagram illustrates the process to update the training data and trigger model retraining.

[![img](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image046.png)](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2022/07/21/ML-8551-image046.png)

The process includes the following steps:

1. The data producer updates the data product with either a new schema or additional data at a regular frequency.
2. After the data product is re-registered in the central data catalog, this generates an [Amazon CloudWatch](http://aws.amazon.com/cloudwatch) event from Lake Formation.
3. The CloudWatch event triggers an

   AWS Lambda

   function to synchronize the updated data product with the consumer account. You can use this trigger to reflect the data changes by doing the following:
   1. Rerun the AWS Glue crawler.
   2. Trigger model retraining if the data drifts beyond a given threshold.

For more details about setting up an SageMaker MLOps deployment pipeline for drift detection, refer to the [Amazon SageMaker Drift Detection](https://github.com/aws-samples/amazon-sagemaker-drift-detection) GitHub repo.

### Auto-deletion of intermediate data in Amazon S3

You can automatically delete intermediate data that is generated by Athena queries and stored in Amazon S3 in the consumer account at regular intervals with S3 object lifecycle rules. For more information, refer to [Managing your storage lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html).

### SageMaker Feature Store integration

[SageMaker Feature Store](https://aws.amazon.com/sagemaker/feature-store/) is purpose-built for ML and can store, discover, and share curated features used in training and prediction workflows. A feature store can work as a centralized interface between different data producer teams and LoBs, enabling feature discoverability and reusability to multiple consumers. The feature store can act as an alternative to the central data catalog in the data mesh architecture described earlier. For more information about cross-account architecture patterns, refer to [Enable feature reuse across accounts and teams using Amazon SageMaker Feature Store](https://aws.amazon.com/blogs/machine-learning/enable-feature-reuse-across-accounts-and-teams-using-amazon-sagemaker-feature-store/).

Refer to the [**MLOps foundation roadmap for enterprises with Amazon SageMaker**](https://aws.amazon.com/blogs/machine-learning/mlops-foundation-roadmap-for-enterprises-with-amazon-sagemaker/) blog post to find out more about building an MLOps foundation based on the MLOps maturity model.

## Conclusion

We showcased how you can build and train ML models with a multi-account data mesh architecture on AWS. We described the requirements of a typical financial services organization with multiple LoBs and an ML CoE, and illustrated the solution architecture with Lake Formation and SageMaker. We used the example of a credit risk data product registered in Lake Formation by the consumer banking LoB and accessed by the ML CoE team to train a credit risk ML model with SageMaker.

Each data producer account defines data products that are curated by people who understand the data and its access control, use, and limitations. The data products and the application domains that consume them are interconnected to form the data mesh. The data mesh architecture allows the ML teams to discover and access these curated data products.

Lake Formation allows cross-account access to Data Catalog metadata and underlying data. You can use Lake Formation to create a multi-account data mesh architecture. SageMaker provides an ML platform with key capabilities around data management, data science experimentation, model training, model hosting, workflow automation, and CI/CD pipelines for productionization. You can set up one or more analytics and ML CoE environments to build and train models with data products registered across multiple accounts in a data mesh.