---
layout: post
title: AWS Cost Analysis - Amazon RDS Costs
description: Amazon RDS is a managed database service that makes it easy to spin up, operate and scale relational databases to support any size production infrastructure. RDS is one of Amazon Web Services’ most utilized services and has a complicated billing structure encompassing instance running costs, data transfer costs, and provisioned IOPS and Storage. For more details about RDS, check out the AWS Documentation
date: 2023-12-13 12:00:00 +0300
author: admin
image: '/images/rds.jpg'
image_caption: 'AWS Cost Analysis - Amazon RDS Costs'
tags: [Cloud, DGAC]
featured: false
---
Amazon RDS is a managed database service that makes it easy to spin up, operate and scale relational databases to support any size production infrastructure. RDS is one of Amazon Web Services’ most utilized services and has a complicated billing structure encompassing instance running costs, data transfer costs, and provisioned IOPS and Storage. For more details about RDS, check out the AWS [documentation](https://docs.aws.amazon.com/rds/index.html).

## **How Much Does Amazon RDS Cost?**

Customers using RDS in their AWS environment have many different variables to consider before spinning up their RDS infrastructure:

- Which database engine are you going to use? The database engine can impact everything from your cost model, hourly costs, and whether or not you need to pay for a database license from a third-party vendor.
- Which region will this instance (or serverless deployment) be deployed in? RDS costs vary for hourly instance costs, storage, and IOPS.
- Is this a Single-AZ or Multi-AZ deployment? Using a Multi-AZ deployment reduces the risk of infrastructure failures but will double your costs.
- What type of activity are we expecting from this resource? Should we be prepared for high data transfer costs across AWS Regions? How often do we need to perform manual database backups?

All of these decisions have a profound impact on your AWS pricing for RDS. The following guide describes how to isolate each main RDS cost category using the [Cost and Usage Report (CUR)](https://getstrake.com/blog/cost-and-usage-report-setup) and what these different cost categories mean. If there are any questions about this guide, please let us know in your [GitHub discussions](https://github.com/getstrake/developer-cost-guide/discussions/3).

## **RDS Instance Costs**

In this guide, I am grouping AWS RDS charges from the cost and usage report into two categories: Existence and Utilization Costs:

1. **Existence Cost** - RDS costs that are associated with running an instance and not impacted by resource activity. Examples of existence costs include ‘InstanceUsage’ and provisioned I/O and storage costs.
2. **Utilization Cost** - RDS costs that are directly impacted by resource activity. Examples include data transfer costs, RDS backup storage, and Aurora serverless usage.

## **RDS Existence Costs**

RDS Existence Cost categories combine instance running costs (similar to [EC2 existence cost](https://getstrake.com/blog/aws-cost-analysis-amazon-ec2-costs)) with the provisioned services and capacity required to run your RDS instance. Variables like Database engine, region, and deployment type will impact your existence costs. Existence cost analysis will walk through how to analyze RDS costs in five categories: Instance usage, provisioned I/O, provisioned Storage, Performance Insights, and Database Proxy costs. If you’re looking for RDS pricing you can check out the [Amazon RDS Pricing Page](https://aws.amazon.com/rds/pricing/).

### **RDS Instance Usage**

RDS Instance usage is the cost associated with running an RDS instance. RDS instances can be deployed as a single or multi-az instance and can be covered with a reserved instance. In the following queries, we will first analyze the on-demand costs for a single or multi-az RDS deployment and then perform the same analysis for RDS instances covered by a reserved instance. In the sections below, the queries will analyze differet RDS Instnace pricing models.On-Demand, Single-AZ Instance Usage

The query below will show **single_od_cost** by **[lineItem/ResourceID]**, **[product/instanceType]**, and **[product/region]**. These results will show all RDS instances with a Single-AZ deployment being billed with the on-demand pricing model.

    -Existence Cost
    --Isolate the Single-AZ, OnDemand, running cost for an RDS Instance.
    SELECT [lineItem/ResourceID], [product/instanceType], [product/region], round(sum([lineItem/UnblendedCost]), 4) as single_od_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'Usage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] LIKE '%InstanceUsage:%'
    GROUP BY [lineItem/ResourceID], [product/instanceType], [product/region]
    ORDER BY sum([lineItem/UnblendedCost]);

For the query above, here is some background on the fields being used and what the filter values mean:

- **[lineItem/ResourceID]** - A unique identifier for an RDS instance. You can also use this field in the WHERE clause to narrow the analysis to a single RDS instance.
- **[product/instanceType]** and **[product/region]** - These are used to identify the instance type and region for each RDS Instance. These are two key variables that determine the pricing of an instance.
- **[lineItem/ProductCode]** - *‘AmazonRDS’* is the product code for RDS. Use this field to ensure the analysis focuses on RDS costs.
- **[lineItem/LineItemType]** - ‘*Usage*’ is the value used to find results for on-demand pricing. Make sure to filter on ‘usage’ when identifying on-demand costs.
- **[lineItem/Operation]** - The operation helps narrow down the cost categories. For RDS, ‘*CreateDBInstance*’ is one of the most common operations to capture existence costs.
- **[lineItem/UsageType]** - For this query, we filtered on ‘*%InstanceUsage%*’ to capture everything for a single-az RDS deployment.

### **On-Demand, Multi-AZ Instance Usage**

The query below will show **multi_od_cost** by **[lineItem/ResourceID]**, **[product/instanceType]**, and **[product/region]**. These results will show all RDS instances with a Multi-AZ deployment billed with the on-demand pricing model.

    -Existence Cost
    --Isolate the Multi-AZ, OnDemand, running cost for an RDS Instance.
    SELECT [lineItem/ResourceID], [product/instanceType], [product/region], round(sum([lineItem/UnblendedCost]), 4) as multi_od_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'Usage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] LIKE '%Multi-AZUsage:%'
    GROUP BY [lineItem/ResourceID], [product/instanceType], [product/region]
    ORDER BY sum([lineItem/UnblendedCost]);

There is only one difference in the filters for a Multi-AZ query compared to a Single-AZ query:

- **[lineItem/UsageType]** - The values ‘*%Multi-AZUsage*’ marks an instance as a Multi-AZ deployment.

### **Reserved Instance, Single-AZ Instance Usage**

If your RDS Instance is covered with a Reserved Instance you will want to understand your RDS Reserved Instance pricing. The query below will show **single_reserved_cost** by **[lineItem/ResourceID]**, **[product/instanceType]**, and **[product/region]**. These results will show all RDS instances with a Single-AZ deployment billed with the reserved instance pricing model.

    -Existence Cost
    --Isolate the Single-AZ, Reserved, running cost for an RDS Instance.
    SELECT [lineItem/ResourceID], [product/instanceType], [product/region], round(sum([reservation/EffectiveCost]), 4) as single_reserved_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'DiscountedUsage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] LIKE     '%InstanceUsage:%'    
    GROUP BY [lineItem/ResourceID], [product/instanceType], [product/region]
    ORDER BY sum([reservation/EffectiveCost]);

For the query above, here is some background on the fields used and what the filter values mean:

- Instead of using **[lineItem/UnblendedCost]** to calculate the total cost, this query uses **[reservation/EffectiveCost]**. This field will add together two costs that are critical for accurately tracking RI costs:
- Hourly amortization of any upfront fees: If your team purchases any RIs that include an upfront fee, your analysis must amortize the cost for the duration of the RI contract. For example, $8,760 in upfront fees for a 1-year contract must be amortized to $1 per hour of the year.
- Hourly fee: If your team purchases any RIs that include an hourly fee, you must add this to the amortization of upfront fees.
- **[lineItem/LineItemType]** - ‘*DiscountedUsage*’ is the value used to find results for reserved instance pricing.

### **Reserved Instance, Multi-AZ Instance Usage**

The query below will show **multi_reserved_cost** by **[lineItem/ResourceID]**, **[product/instanceType]**, and **[product/region]**. These results show all RDS instances with a Multi-AZ deployment billed with the reserved instance pricing model.

    -Existence Cost
    --Isolate the Multi-AZ, Reserved, running cost for an RDS Instance.
    SELECT [lineItem/ResourceID], [product/instanceType], [product/region], round(sum([reservation/EffectiveCost]), 4) as multi_reserved_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'DiscountedUsage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] LIKE     '%Multi-AZUsage:%'
    GROUP BY [lineItem/ResourceID], [product/instanceType], [product/region]
    ORDER BY sum([lineItem/UnblendedCost]);

There is only one difference in the filters for a Multi-AZ query compared to a Single-AZ query:

- This query also uses the **[reservation/EffectiveCost]** field to calculate the total cost.
- **[lineItem/UsageType]** - The values ‘*%Multi-AZUsage*’ denotes the instance is a Multi-AZ deployment.

### **Total RDS Instance Usage Cost**

The query below combines Single-AZ On-Demand, Single-AZ Reserved, Multi-AZ On-Demand, and Multi-AZ Reserved RDS instance costs. The final column of this query, **total_running_cost**, combines all the different cost types.

    -Existence Cost
    --Total instance running costs combining Single, Multi-AZ on-demand and Reserved instance costs
    WITH single_od_cost AS (
    SELECT [lineItem/ResourceID], [product/instanceType], [product/region], round(sum([lineItem/UnblendedCost]), 4) as single_od_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'Usage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] LIKE '%InstanceUsage:%'
    GROUP BY [lineItem/ResourceID], [product/instanceType], [product/region]
    ORDER BY sum([lineItem/UnblendedCost])
    )
    , single_reserved_cost AS (
    SELECT [lineItem/ResourceID], [product/instanceType], [product/region], round(sum([reservation/EffectiveCost]), 4) as single_reserved_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'DiscountedUsage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] LIKE     '%InstanceUsage:%'
    GROUP BY [lineItem/ResourceID], [product/instanceType], [product/region]
    ORDER BY sum([reservation/EffectiveCost])
    )
    , multi_od_cost AS (
    SELECT [lineItem/ResourceID], [product/instanceType], [product/region], round(sum([lineItem/UnblendedCost]), 4) as multi_od_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'Usage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] LIKE '%Multi-AZUsage:%'
    GROUP BY [lineItem/ResourceID], [product/instanceType], [product/region]
    ORDER BY sum([lineItem/UnblendedCost])
    )
    , multi_reserved_cost AS (
    SELECT [lineItem/ResourceID], [product/instanceType], [product/region], round(sum([reservation/EffectiveCost]), 4) as multi_reserved_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'DiscountedUsage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] LIKE     '%Multi-AZUsage:%'
    GROUP BY [lineItem/ResourceID], [product/instanceType], [product/region]
    ORDER BY sum([lineItem/UnblendedCost])
    )
    SELECT CUR.[lineItem/ResourceID], CUR.[product/instanceType], CUR.[product/region], COALESCE(single_od_cost.single_od_cost, 0) as single_od_cost, COALESCE(single_reserved_    cost.single_reserved_cost, 0) as single_reserved_cost, COALESCE(multi_od_cost.multi_od_cost, 0) as multi_od_cost, COALESCE(multi_reserved_cost.multi_reserved_cost, 0) as multi_    reserved_cost, (COALESCE(single_od_cost.single_od_cost, 0)+COALESCE(single_reserved_cost.single_reserved_cost, 0)+COALESCE(multi_od_cost.multi_od_cost, 0)+COALESCE(multi_reserved_    cost.multi_reserved_cost, 0)) as total_running_cost
    FROM CUR
    LEFT JOIN single_od_cost ON single_od_cost.[lineItem/ResourceID] = CUR.[lineItem/ResourceID]
    LEFT JOIN single_reserved_cost ON single_reserved_cost.[lineItem/ResourceID] = CUR.[lineItem/ResourceID]
    LEFT JOIN multi_od_cost ON multi_od_cost.[lineItem/ResourceID] = CUR.[lineItem/ResourceID]
    LEFT JOIN multi_reserved_cost ON multi_reserved_cost.[lineItem/ResourceID] = CUR.[lineItem/ResourceID]
    WHERE CUR.[lineItem/ProductCode] is 'AmazonRDS' and CUR.[product/instanceType] <> "" and CUR.[lineitem/ResourceId] <> ""
    GROUP BY CUR.[lineitem/ResourceId], CUR.[product/instanceType], CUR.[product/region]
    ORDER BY total_running_cost;

### **Provisioned Storage**

### **Provisioned General Purpose Storage**

RDS instance storage comes in many different formats. With General Purpose provisioned storage, you are only charged for the provisioned storage and not any of the IOPS consumed. Storage fees are charged as $ per GB per month. In this query, total cost is isolated as **gp2_storage_cost** by filtering **[lineItem/UsageType]** with the value ‘*%GP2-Storage%*’.

    -Existence Cost
    --Provisioned GP2 Storage
    SELECT [lineItem/ResourceID], round(sum([lineItem/UnblendedCost]), 4) as gp2_storage_cost
    FROM cur
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'Usage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] LIKE '%GP2-Storage%'
    GROUP BY [lineitem/ResourceID]
    ORDER BY sum([lineItem/UnblendedCost]);

### **Provisioned IOPS Storage**

RDS allows users to provision I/O capacity required for your project. You will be charged a fixed amount for the storage provisioned and the IOPS consumed. To narrow down RDS costs to provisioned IOPS, filter the **[lineItem/UsageType]** to ‘*%PIOPS%*’. In this query, we are calculating the total cost as **piops_cost**.

    -Existence Cost
    --Provisioned IOPS (PIOPS) and Storage.
    SELECT [lineItem/ResourceID], round(sum([lineItem/UnblendedCost]), 4) as piops_cost
    FROM cur
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'Usage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] LIKE '%PIOPS%'
    GROUP BY [lineitem/ResourceID]
    ORDER BY sum([lineItem/UnblendedCost]);

### **Provisioned Magnetic Storage**

Magnetic storage is a previous generation of storage. AWS recommends upgrading to GP2 or GP3 storage if you currently use Magnetic storage in your infrastructure. That being said, we can identify **magnetic_storage_cost** by filtering the **[lineItem/ResourceId]** by ‘*%StorageUsage*’.

    -Existence Cost
    --Provisioned Magnetic Storage
    SELECT [lineItem/ResourceID], round(sum([lineItem/UnblendedCost]), 4) as magnetic_storage_cost
    FROM cur
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'Usage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] LIKE '%StorageUsage'
    GROUP BY [lineitem/ResourceID]
    ORDER BY sum([lineItem/UnblendedCost]);

### **Performance Insights**

Performance Insights for RDS offer free 7-day retention of performance data and 1M API requests per month for your RDS instance at no charge. From there, users can decide to extend the retention up to 24 months for a fixed cost per vCPU. In this query, total performance insights cost is calculated as **pi_cost** by filtering **[lineItem/Operation]** as ‘*Retention*’ and **[lineItem/UsageType]** as ‘*%PI%*’.

Note: this is only the cost for retention of performance insights data. We will query the cost of API calls for performance insights in the Utilization section.

-Existence Cost
--Performance Insights Retention Usage
SELECT [lineitem/ResourceID], round(sum([lineItem/UnblendedCost]), 4) as pi_cost
FROM cur
WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'Usage' and [lineItem/Operation] LIKE 'Retention' and [lineItem/UsageType] LIKE '%PI%'
GROUP BY [lineitem/ResourceID]
ORDER BY sum([lineItem/UnblendedCost]);`

### **Database Proxy**

The RDS Proxy is a fully managed, highly available database proxy that makes your application more scalable and resilient to failure. The cost of a DB Proxy is based on the capacity of the underlying instances. The total cost is calculated as **db_proxy_cost** by filtering **[lineItem/Operation]** as ‘*CreateDBProxy%*’ and **[lineItem/UsageType]** as ‘*%ProxyUsage*’.

    -Existence Cost
    --RDS Proxy Usage
    SELECT [lineitem/ResourceID], round(sum([lineItem/UnblendedCost]), 4) as db_proxy_cost
    FROM cur
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'Usage' and [lineItem/Operation] LIKE 'CreateDBProxy%' and [lineItem/UsageType] LIKE '%ProxyUsage'
    GROUP BY [lineitem/ResourceID]
    ORDER BY sum([lineItem/UnblendedCost]);

## **RDS Utilization Cost**

The activity of your RDS instance determines RDS Utilization costs. The most common types of Utilization costs for RDS instances are data transfer and backup storage costs. In addition to these categories, there are also Utilization costs for Serverless Aurora RDS usage.

### **Data Transfer Costs**

Data transfer costs include sending data from your RDS instance out of AWS entirely and sending data between different AWS Services or regions. Small operational changes can cause massive data transfer costs if not done correctly. The queries below will provide analysis for isolating the different types of data transfer costs.

### **Data Transfer for RDS to/from the Internet**

Data transfer costs for RDS to/from the internet are charged based on the GB of data being transferred. There is no cost for bringing data from the internet into your AWS environment. There is a tiered pricing model for moving data to the internet, meaning the more data sent, the lower the unit cost.

This query below calculates the **data_transfer_cost** by filtering the **[lineItem/UsageType]** to ‘*DataTransfer-%*’. The field **data_transfer_gb** is also in this query so you can see the amount of data being transferred when there is no cost.

    -Utilization
    --Data Transfer for RDS to/from the Internet
    SELECT [lineitem/ResourceID], [lineItem/UsageType], round(sum([lineItem/UsageAmount]), 4) as data_transfer_gb, round(sum([lineItem/UnblendedCost]), 4) as data_transfer_cost
    FROM cur
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] IS 'Usage' and [lineItem/Operation] IS 'Not Applicable' and [lineItem/UsageType] LIKE '%DataTransfer-%'
    GROUP BY [lineitem/ResourceID], [lineItem/UsageType]
    ORDER BY sum([lineItem/UnblendedCost]);

### **Data Transfer for RDS to/from AWS Service or Region**

If your team sends data between different AWS services or Regions, there may also be costs for that. Using the query below you can calculate the **aws_data_transfer_cost** by isolating **[lineItem/UsageType]** fields that contain ‘*%-aws-%*’. I also added the field **aws_data_transfer_gb** to calculate the data transfer volume.

    -Utilization
    --Data Transfer for RDS to/from AWS Service or Region
    SELECT [lineitem/ResourceID], [lineItem/UsageType], round(sum([lineItem/UsageAmount]), 4) as aws_data_transfer_gb, round(sum([lineItem/UnblendedCost]), 4) as aws_data_transfer_cost
    FROM cur
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] IS 'Usage' and [lineItem/Operation] IS 'Not Applicable' and [lineItem/UsageType] LIKE '%-AWS-%'
    GROUP BY [lineitem/ResourceID], [lineItem/UsageType]
    ORDER BY sum([lineItem/UnblendedCost]);

### **Backup Storage**

Backup storage is the cost associated with automated and manual database backups. These costs are charged at a fixed rate of $ per GB per month based on the region the backups are stored in. This query calculates the **backup-storage-cost** by isolating [lineItem/UsageType] values that contain ‘*%ChargedBackupUsage%*’.

    -Utilization Cost
    --Charged backup usage
    SELECT [lineitem/ResourceID], round(sum([lineItem/UsageAmount]), 4) as backup_storage_db, round(sum([lineItem/UnblendedCost]), 4) as backup_storage_cost
    FROM cur
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] is 'Usage' and [lineItem/UsageType] LIKE '%ChargedBackupUsage%'
    GROUP BY [lineitem/ResourceID]
    ORDER BY sum([lineItem/UnblendedCost]);

### **Aurora Consumption Billing**

Amazon Aurora allows customers to run Aurora Serverless or to provision an instance for their workloads. Serverless Aurora, Aurora Storage, and I/O have usage-based billing models. The queries below will show how to identify each of these costs.

### **Aurora Serverless**

Aurora has a serverless offering from AWS that is billed per usage second. This model is great for highly dynamic workloads that don’t require an instance to run at all times. Everything is priced based on usage. The query below compiles the Aurora Serverless costs as **aurora_serverless_cost** and is identified by isolating **[lineItem_UsageType]** values that contain ‘*Aurora:Serverless%*’.

    -Utilization
    --Aurora Serverless Usage
    SELECT [lineitem/ResourceID], round(sum([lineItem/UnblendedCost]), 4) as aurora_serverless_cost
    FROM cur
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] IS 'Usage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] LIKE     'Aurora:Serverless%'
    GROUP BY [lineitem/ResourceID]
    ORDER BY sum([lineItem/UnblendedCost]);

### **Aurora Storage**

Regardless of whether a customer chooses Aurora Serverless or a provisioned database instance, the Storage costs are driven by usage. These costs can be isolated by filtering the **[lineItem/UsageType]** field for ‘*Aurora:StorageUsage*’ values.

    -Utilization
    --Aurora Storage utilization costs
    SELECT [lineitem/ResourceID], [lineItem/UsageType], round(sum([lineItem/UnblendedCost]), 4) as storage_usage_cost
    FROM cur
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] IS 'Usage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] IS 'Aurora:StorageUsage'
    GROUP BY [lineitem/ResourceID], [lineItem/UsageType]
    ORDER BY sum([lineItem/UnblendedCost]);

### **Aurora I/O**

Whether a customer chooses Aurora Serverless or a provisioned database instance, the I/O costs are based on usage. These costs can be isolated by filtering the **[lineItem/UsageType]** field for ‘*Aurora:StorageIOUsage*’ values.

    -Utilization
    --Aurora I/O utilization costs
    SELECT [lineitem/ResourceID], [lineItem/UsageType], round(sum([lineItem/UnblendedCost]), 4) as io_usage_cost
    FROM cur
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] IS 'Usage' and [lineItem/Operation] LIKE 'CreateDBInstance%' and [lineItem/UsageType] IS     'Aurora:StorageIOUsage'
    GROUP BY [lineitem/ResourceID], [lineItem/UsageType]
    ORDER BY sum([lineItem/UnblendedCost]);

### **API Requests for Performance Insights**

We analyzed the retention costs for performance insights in the existence cost section. The second cost associated with performance insights is the cost per API call. The first 1M API calls are free; after that, the API calls are charged at $0.01 per 1,000 API calls. The count of API calls and the associated costs can be isolated by filtering the **[lineItem/Operation]** field as ‘*Call*’ and the **[lineItem/UsageType]** field by ‘*%API%*’.

    -Utilization
    --API Calls for performance insights
    SELECT  [lineitem/ResourceID],  round(sum([lineItem/UsageAmount]), 4) as api_call_count,    round(sum([lineItem/UnblendedCost]), 4) as api_call_cost
    FROM cur
    WHERE   [lineItem/ProductCode] = 'AmazonRDS'    and [lineItem/LineItemType] IS 'Usage'  and [lineItem/Operation] IS 'Call'  and [lineItem/UsageType] LIKE '%API%'
    GROUP BY    [lineitem/ResourceID]
    ORDER BY    sum([lineItem/UnblendedCost]);

## **Total RDS Instance Cost**

All the queries until now have been used to isolate specific cost categories for RDS resources. This next section will walk through how to isolate specific instances to understand their costs over time. The table below can be used as a reference guide for mapping **[lineItem/LineItemType]** and **[lineItem/Operation]** to cost categories with an explanation of which costs are captured there.

| [lineItem/UsageType] | [lineItem/Operation] | Cost Category | Notes |
| --- | --- | --- | --- |
| InstanceUsage:% | CreateDBInstance | Existence | Instance Usage. Instance type will be added after ‘:’. Region code can be added at the beginning for international usage. |
| Multi-AZUsage:% | CreateDBInstance | Existence | Multi-AZ Usage instance usage. Instance type will be added after ‘:’. Region code can be added at the beginning for international usage. |
| RDS:GP2-Storage | CreateDBInstance | Existence | GP2 RDS instance storage. |
| RDS:Multi-AZ-GP2-Storage | CreateDBInstance | Existence | Multi-AZ GP2 instance storage. |
| RDS:Multi-AZ-PIOPS | CreateDBInstance | Existence | Multi-AZ provisioned IOPS |
| RDS:Multi-AZ-PIOPS-Storage | CreateDBInstance | Existence | Multi-AZ provisioned IOPS storage |
| RDS:PIOPS | CreateDBInstance | Existence | Provisioned IOPS |
| RDS:PIOPS-Storage | CreateDBInstance | Existence | Provisioned IOPS storage |
| RDS:StorageUsage | CreateDBInstance | Existence | Provisioned magnetic storage (AWS recommends customers should upgrade to GP2). |
| USE1-RDS:ProxyUsage | CreateDBProxy:0002 | Existence | Database Proxy costs. |
| USE1:PI_LTR:T3 | Retention | Existence | Performance insights retention. |
| HeavyUsage:% | CreateDBInstance | RI Fee | RI Fees and reservation information. |
| USE1:PI_API | Call | Utilization | API Requests for Performance Insights. |
| DataTransfer-In-Bytes | Not Applicable | Utilization | Data Transfer in from the internet to RDS. |
| DataTransfer-Out-Bytes | Not Applicable | Utilization | Data Transfer out from RDS to the internet. |
| USE2-USE1-AWS-In-Bytes | Not Applicable | Utilization | Data Transfer within AWS between regions. This example has traffic coming ‘In’ between us-east-2 and us-east-1. |
| USE2-USE1-AWS-Out-Bytes | Not Applicable | Utilization | Data Transfer within AWS between regions. This example has traffic going ‘out’ between us-east-2 and us-east-1. |
| Aurora:ServerlessV2Usage% | CreateDBInstance | Utilization | Aurora serverless usage |
| Aurora:StorageIOUsage | CreateDBInstance | Utilization | Aurora I/O charged based on usage. |
| Aurora:StorageUsage | CreateDBInstance | Utilization | Aurora Storage charged based on usage. |
| RDS:ChargedBackupUsage | CreateDBInstance | Utilization | Storage for RDS backups. |
| RDS:ChargedBackupUsage | Not Applicable | Utilization | Storage for RDS backups. |

### **Total RDS Instance Cost**

### **Total Cost by Category**

The following query will show total cost by **[lineItem/LineItemType]**, **[lineItem/Operation]**, and **[lineItem/UsageType]**. Since we potentially include costs from reserved instances, we will have to sum both the **[lineItem/Unblendedcost]** and **[reservation/EffectiveCost]** fields. In addition to total costs, since some types of usage are not billed, we also include **[lineItemUsageAmount]** and the **[pricing/unit]** to provide visibility into usage.

This query is limited to analyzing RDS costs and removes any ‘Tax’ expenses from the results.

    -Total RDS Cost By Category
    --Total cost by line item type, operation and usage type.
    SELECT [lineItem/LineItemType], [lineItem/Operation], [lineItem/UsageType], round(sum([lineItem/UsageAmount]), 4) as usage_amount, [pricing/unit], round(sum([lineItem/UnblendedCost]    ), 4) as unblended_cost, round(sum([reservation/EffectiveCost]), 4) as reserved_cost
    FROM cur
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] <> 'Tax'
    GROUP BY [lineItem/LineItemType], [lineItem/Operation], [lineItem/UsageType], [pricing/unit]
    ORDER BY [lineItem/LineItemType], [lineItem/Operation], [lineItem/UsageType], sum([lineItem/UnblendedCost]);

With this base query understood, two variations are helpful depending on the specific questions that need to be answered:

### **Total Cost by RDS Instance**

We can see the total cost by instance and resource category if we take the base query and add **[lineItem/ResourceID]** in the SELECT, GROUP BY, and ORDER BY statements. This query can return a significant number of search results but is helpful when trying to understand which instances are driving your costs.

    -Total RDS Cost by Resource ID and Category
    --Total cost by line item type, operation and usage type.
    SELECT [lineItem/ResourceId], [lineItem/LineItemType], [lineItem/Operation], [lineItem/UsageType], round(sum([lineItem/UsageAmount]), 4) as usage_amount, [pricing/unit], round(sum([    lineItem/UnblendedCost]), 4) as unblended_cost, round(sum([reservation/EffectiveCost]), 4) as reserved_cost
    FROM cur
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] <> 'Tax'
    GROUP BY [lineItem/ResourceId], [lineItem/LineItemType], [lineItem/Operation], [lineItem/UsageType], [pricing/unit]
    ORDER BY [lineItem/ResourceId], [lineItem/LineItemType], [lineItem/Operation], [lineItem/UsageType], sum([lineItem/UnblendedCost]);

### **Total Cost by RDS Instance by Hour**

Once you understand which RDS Instances are costing the most in your environment, you can simply add the **[lineItem/UsgaeStartDate]** field into your query to track costs over time. To isolate the cost of a single RDS instance, remove the **[lineItem/ResourceID]** from the SELECT, GROUP BY, and ORDER BY statements and add it to the WHERE statement with a resource ID. Replace the variable *insert-rds-are-here* with the ARN of your RDS instance.

    -Total RDS Cost by Resource ID and Hour
    --Total cost by line item type, operation and usage type.
    SELECT [lineItem/UsageStartDate], [lineItem/LineItemType], [lineItem/Operation], [lineItem/UsageType], round(sum([lineItem/UsageAmount]), 4) as usage_amount, [pricing/unit], round(    sum([lineItem/UnblendedCost]), 4) as unblended_cost, round(sum([reservation/EffectiveCost]), 4) as reserved_cost
    FROM cur
    WHERE [lineItem/ProductCode] = 'AmazonRDS' and [lineItem/LineItemType] <> 'Tax' and [lineItem/ResourceId] is 'insert-rds-arn-here'
    GROUP BY [lineItem/UsageStartDate], [lineItem/LineItemType], [lineItem/Operation], [lineItem/UsageType], [pricing/unit]
    ORDER BY [lineItem/UsageStartDate], [lineItem/LineItemType], [lineItem/Operation], [lineItem/UsageType], sum([lineItem/UnblendedCost]);