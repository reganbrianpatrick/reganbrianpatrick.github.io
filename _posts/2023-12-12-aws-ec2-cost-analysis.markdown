---
layout: post
title: AWS Cost Analysis - Amazon EC2 Costs
description: Elastic Compute Cloud (EC2) is the backbone of AWS. EC2 allows customers to access hundreds of different instance types across the globe in seconds.
date: 2023-12-12 12:00:00 +0300
author: admin
image: '/images/ec2.jpg'
image_caption: 'AWS Cost Analysis - Amazon EC2 Costs'
tags: [cloud, dgac]
featured: false
---
[Elastic Compute Cloud (EC2)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) is the backbone of AWS. EC2 allows customers to access hundreds of different instance types across the globe in seconds.

## **How Much Does Amazon EC2 Cost?**

EC2 is entirely usage based, meaning you can turn instances on and off as needed and only pay by the hour. EC2 costs are complicated because of the different pricing models: on-demand, spot, reserved instances, and savings plans. In addition, there are many types of cost for EC2 instances: hourly RunInstance charges, data transfer, and NAT gateway costs.

This guide walks through the different types of EC2 costs and how to isolate them using SQL analysis on your Cost and Usage Report (CUR). If you don’t have a CUR active in your account, you can check out [the first guide](https://eightlake.com/cost-and-usage-report-setup) which walks through permissions and the process of creating a CUR and our blog on [cost and usage report documentation](https://eightlake.com/aws-cost-and-usage-report-documentation).

## **EC2 Cost Categories**

To simplify EC2 cost analysis, costs are grouped into three categories: Existence, Utilization and Subresource Cost:

1. **Existence Cost** - *EC2-Instances* in cost explorer - This identifies the cost of having an EC2 instance running regardless of the resource activity. This cost is based on an hourly rate which is determined by the combination of your instance type, region, and whether you’re using contractual reservations or spot instances.
2. **Utilization Cost** - *part of* *EC2 - Other* in cost explorer - On top of the flat rate for having an instance running, some costs come from how an EC2 instance is utilized. Examples of utilization costs include data transfer and NAT Gateway fees. These costs are driven by a usage-based metric such as $ per GB-month.
3. **Subresource Cost** - *the second part of* *EC2 - Other* in cost explorer - The types of costs that can be incurred for resources that are attached to or created by your EC2 instances. An easy example for EC2 is EBS Volumes used for storage.

## **Existence Cost**

Existence costs are the most simple EC2 costs to understand and calculate. Every hour that an EC2 instance is running, there is a cost charged to your account. This cost does not change based on how your resource is used. The AWS [pricing page for EC2](https://aws.amazon.com/ec2/pricing/) is a great resource for looking up instance costs based on the pricing model, Region, Operating system, and Instance type.

To isolate EC2 Existence Costs in the Cost and Usage Report, you must analyze different fields for the different pricing models: On-Demand, Spot, Reserved Instances, and Savings Plans. The following analysis will walk through how to isolate costs for each pricing model and then combine the analysis to provide a total EC2 Existence Cost.

### **On-Demand Existence Cost**

On-demand pricing is the published market rate for EC2 usage. If you’re looking for On-demand instance pricing, check out the [Amazon EC2 Instance pricing page](https://aws.amazon.com/ec2/pricing/on-demand/).

The benefit of on-demand pricing is that there is no commitment for usage and guaranteed availability. Every instance type (instance family + instance size) has a published hourly rate by region.

The query below can be used to identify the on-demand existence costs by instance type and region:

    -On-Demand existence cost
    --On-demand existence cost by instance type & region
    SELECT [product/instanceType], [product/region], round(sum([lineItem/UnblendedCost]), 4) as existence_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product/instanceType] <> "" and [lineItem/LineItemType] is 'Usage' and [lineItem/UsageType] LIKE '%BoxUsage%' and [lineItem/Operation    ] LIKE 'RunInstances%'
    GROUP BY [product/instanceType], [product/region]
    ORDER BY sum([lineItem/UnblendedCost]);

Most of the fields in this first analysis are repeatedly used throughout this guide. Below are details on the fields and what the filtered values mean. If there are more questions about any of the fields in the CUR the [AWS CUR Data Dictionary](https://docs.aws.amazon.com/cur/latest/userguide/data-dictionary.html) is a pretty good resource.

- **[product/instanceType]** will show the EC2 instance type. For example, ‘m5.4xlarge’ and ‘t2.large’. This field cannot be blank for tracking Existence costs.
- **[product/region]** is the region an instance is running in. For example, ‘us-east-1’ and ‘ap-northeast-2’.
- **[lineItem/UnblendedCost]** is the most commonly used cost field in the CUR. This field takes the hourly rate for that period, *[lineItem/UnblendedRate]*, and multiplies it by the usage for that period, *[lineItem/UsageAmount]*. This cost being unblended means we are looking at the specific cost for each resource, during each time period on the CUR. This is the most accurate way to isolate your costs for on-demand instances.
- **[lineItem/ProductCode]** is used to identify the AWS product being measured. ‘AmazonEC2’ is the product code for EC2.
- **[lineItemLineItemType]** will show the type of charge that is hitting this line item. There are line item types for everything from ‘Usage’ to ‘Tax’ and ‘Refund’. ‘Usage’ will indicate this charge is either for on-demand or spot instances.
- **[lineItem/UsageType]**, according AWS documentation, shows details on the line item. For this analysis specifically, the usage type field will show whether the usage is on-demand or spot usage. *%BoxUsage%* indicates this line item was billed with the on-demand pricing model.
- **[lineitem/Operation]** details the AWS Operation covered by a line item. For EC2, ‘RunInstances’ tells us this is the operation of running an EC2 instance. If you require details down to the specific operating system, the codes that come after ‘RunInstances’ can be used to filter down to [specific AMIs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/billing-info-fields.html).

Depending on the size of your AWS environment you may also want to see a list of all the active EC2 resources and their associated costs. Any time you want to track individual resources, add in the **[lineItem/ResourceID]** field:

    -On-Demand existence cost
    --On-demand existence cost by resource ID
    SELECT [lineItem/ResourceId], [product/region], round(sum([lineItem/UnblendedCost]), 4) as existence_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product/instanceType] <> "" and [lineItem/LineItemType] is 'Usage' and [lineItem/UsageType] LIKE '%BoxUsage%' and [lineItem/Operation    ] LIKE 'RunInstances%'
    GROUP BY [lineItem/ResourceId], [product/region]
    ORDER BY sum([lineItem/UnblendedCost]);

### **Spot Existence Cost**

Spot instances are priced based on a secondary market for EC2 usage. Spot pricing can provide a massive discount (upwards of 90%) compared to on-demand pricing. Unfortunately, instances are not always available, and the pricing will change based on market demand.

Most production architectures will avoid utilizing spot instances since they shut off when there is no availability. Under the right circumstances, Spot instances are an awesome opportunity for savings.

The query below can be used to identify the spot existence costs by instance type and region:

    -Spot existence cost
    --Spot existence cost by instance type, region
    SELECT [product/instanceType], [product/region], round(sum([lineItem/UnblendedCost]), 4) as existence_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product/instanceType] <> "" and [lineItem/LineItemType] is 'Usage' and [lineItem/UsageType] LIKE '%SpotUsage%' and [lineItem/    Operation] LIKE 'RunInstances%'
    GROUP BY [product/instanceType], [product/region]
    ORDER BY sum([lineItem/UnblendedCost]);

The only major difference between the on-demand and spot queries is the **[lineItem/UsageType]** and **[lineItem/Operation]** fields. The [lineItem/Operation] field will show *RunInstances:SV%* and [lineItem/UsageType] will show *%SpotUsage%* to indicate these line items are attributed to spot instances.

The query above will show the total blended cost, but since the spot instance rates can change hourly, you may want to pull detail on how instance unit costs have changed over time. To see the variance in hourly or daily spot instance rates, use the query below:

    -Spot existence cost
    --Spot existence by usage start date, instance type, region
    SELECT [lineItem/UsageStartDate], [product/instanceType], [product/region], [lineItem/UnblendedCost]/[lineItem/UsageAmount] as spot_rate
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product/instanceType] <> "" and [lineItem/LineItemType] is 'Usage' and [lineItem/UsageType] LIKE '%SpotUsage%' and [lineItem/    Operation] LIKE 'RunInstances:SV%'
    GROUP BY [product/instanceType], [lineItem/UsageStartDate], [product/region]
    ORDER BY [lineItem/UnblendedCost]/[lineItem/UsageAmount];

There are two meaningful changes to this query:

- **[lineItem/UsageStartDate]** was added and will show the day and hour when the instance usage started. This will add a lot of detail to your query result, but this can be useful for tracking unit costs over time for a specific instance type and region.
- The CUR doesn’t calculate any rate fields for spot instance usage. To address this, we have a calculated field **spot_rate** that calculates a rate based on the **[lineItem/UnblendedCost]** and the **[lineItem/UsageAmount]**. If you group by instance type and region, this will show the average rate for that instance type.

### **Reserved Instance Existence Cost**

Reserved Instances (RIs) are a tool offered by AWS that allows customers to reduce their spending on specific services if they make long term commitments. Term commitments for EC2 Reserved Instances are either 1-year or 3-years. Like all other AWS discounts, discount size increases with usage commitments and specificity of use.

RIs on EC2 instances are very common, but cost tooling offered by AWS obfuscates exactly what your RIs are covering and what the real costs are.

Reserved instance costs are not allocated to instances in AWS’ Cost Explorer. If your team uses reserved instances, the costs shown for those instances will be $0, which is obviously not true. The query below outlines how to see what types of resources your reserved instance contracts are covering and the associated costs.

    -Reserved instance existence cost
    --Reserved instance existence cost by instance type, region
    SELECT [product/instanceType], [product/region], round(sum([reservation/EffectiveCost]), 4) as existence_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product/instanceType] <> "" and [lineItem/LineItemType] is 'DiscountedUsage'
    GROUP BY [product/instanceType], [product/region]
    ORDER BY sum([reservation/EffectiveCost]);

There are several changes to how you isolate RI costs compared to on-demand and spot costs:

- For **[lineItem/LineItemType]**, the value denoting the use of reserved instances is *‘DiscountedUsage’* instead of *‘Usage’* that we saw for on-demand and spot instances.
- Instead of using **[lineItem/UnblendedCost]** for the total cost, this query uses **[reservation/EffectiveCost]**. This field will add together two costs that are critical for accurately tracking RI costs:
- Hourly amortization of any upfront fees: If your team purchases any RIs that include an upfront fee, that cost needs to be spread out over the duration of the RI contract. For example, if you pay $8,760 in upfront fees for a 1-year contract, that is amortized to $1 per hour for every hour of the year (there are 8,760 hours in 1 year).
- Hourly fee: If your team purchases any RIs that include an hourly fee, this needs to be added into the amortization of upfront fees.

To provide a little more detail, here are the different payment options offered for AWS Reservations and whether the payment options include upfront and hourly fees. The more upfront fees a customer commits to, the higher the discount on the reservation contract.

| Payment Options | Upfront Fees? | Hourly Fees? |
| --- | --- | --- |
| All Upfront | Yes | No |
| Partial Upfront | Yes | Yes |
| No Upfront | No | Yes |

To keep track of all the different reservations in an account or group of accounts, every reservation has a unique [Amazon Resource Name (ARN)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html). The field **[reservation/ReservationARN]** can be added into the analysis to show the count and total effective cost of reservations active in your account. If there is a specific reservation you want details on, the **[reservation/ReservationARN]** field can be added to the *WHERE* clause with a specific reservation ARN or a list of reservation ARNs.

    -Reserved instance existence cost
    --Reserved instance ARN information
    SELECT [reservation/ReservationARN], [product/instanceType], [product/region], round(sum([reservation/EffectiveCost]), 4) as existence_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product/instanceType] <> "" and [lineItem/LineItemType] is 'DiscountedUsage'
    GROUP BY [reservation/ReservationARN], [product/instanceType], [product/region]
    ORDER BY sum([reservation/EffectiveCost]);

### **Savings Plan Existence Cost**

Savings plans are AWS’ latest and greatest reservation tool. There are some serious benefits to savings plans compared to reserved instances, to name a few: compute savings plans work cross-region and cover serverless compute services like Fargate and Lambda in addition to EC2. For the purposes of this guide, there is no difference between the cost analysis for EC2 and Compute Savings plans.

The analysis for Savings Plan existence cost looks very similar to reserved instance existence costs, but we will filter the fields on slightly different values. This first query will show you what resources your savings plans are covering and their costs:

    -Savings plan existence cost
    --Savings plan existence cost by instance type, region
    SELECT [product/instanceType], [product/region], round(sum([savingsPlan/SavingsPlanEffectiveCost]), 4) as existence_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product/instanceType] <> "" and [lineItem/LineItemType] is 'SavingsPlanCoveredUsage'
    GROUP BY [product/instanceType], [product/region]
    ORDER BY sum([savingsPlan/SavingsPlanEffectiveCost]);

There are again changes to the fields and values we filter on for Savings Plan costs:

- Similar to the **[reservation/EffectiveCost]** field, the **[savingsPlan/SavingsPlanEffectiveCost]** field will combine the upfront and hourly fees associated with savings plans. Savings Plans and Reserved Instances have the same payment options, so the upfront and hourly fee combinations are the same.
- For **[lineItem/LineItemType]** the value *‘SavingsPlanCoveredUsage’* will identify any line item that a Savings Plan covers.

Similar to Reserved Instances, Savings Plans also have an Amazon Resource Name (ARN) that can be tracked on the Cost and Usage Report. The query below will isolate all active Savings Plans reservations using the **[savingsPlan/SavingsPlanARN]**.

    -Savings plan existence cost
    --Savings plan ARN information
    SELECT [savingsPlan/SavingsPlanARN], [product/region], round(sum([savingsPlan/SavingsPlanEffectiveCost]), 4) as existence_cost
    FROM CUR
    WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product/instanceType] <> "" and [lineItem/LineItemType] is 'SavingsPlanCoveredUsage'
    GROUP BY [savingsPlan/SavingsPlanARN], [product/region]
    ORDER BY sum([savingsPlan/SavingsPlanEffectiveCost]);

### **Total Existence Cost for EC2**

The final step for understanding the Existence Cost for EC2 is to bring all the pricing models together to get a **total EC2 existence cost**. This query is handy when trying to understand what percentage of your infrastructure is covered with reservations, which regions you’re spending the most money in, and which EC2 instance types your teams are using most.

The queries below will show all resource IDs, the instance type and region of that resource, a calculated column for each EC2 existence cost type, and a calculated field for **TotalExistenceCost**.
```
    -Total existence cost for EC2
    --Total existence cost by resource ID
    WITH on_demand_existence AS ( SELECT [lineItem/ResourceId], round(sum([lineItem/UnblendedCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] = 'AmazonEC2' and [    product/instanceType] <> "" and [lineItem/LineItemType] is 'Usage' and [lineItem/UsageType] LIKE '%BoxUsage%' and [lineItem/Operation] LIKE 'RunInstances%' GROUP BY [lineItem/    ResourceId]
    )
    , spot_existence AS ( SELECT [lineItem/ResourceId], round(sum([lineItem/UnblendedCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product/    instanceType] <> "" and [lineItem/LineItemType] is 'Usage' and [lineItem/UsageType] LIKE '%SpotUsage%' and [lineItem/Operation] LIKE 'RunInstances%' GROUP BY [lineItem/ResourceId]
    )
    , reserved_existence AS ( SELECT [lineItem/ResourceId], round(sum([reservation/EffectiveCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product    /instanceType] <> "" and [lineItem/LineItemType] is 'DiscountedUsage' GROUP BY [lineItem/ResourceId]
    )
    , savings_plan_existence AS ( SELECT [lineItem/ResourceId], round(sum([savingsPlan/SavingsPlanEffectiveCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] =     'AmazonEC2' and [product/instanceType] <> "" and [lineItem/LineItemType] is 'SavingsPlanCoveredUsage' GROUP BY [lineItem/ResourceId]
    )
    SELECT CUR.[lineitem/ResourceId], CUR.[product/instanceType], CUR.[product/region], COALESCE(on_demand_existence.existence_cost, 0) as on_demand_existence_cost, COALESCE(spot_    existence.existence_cost, 0) as spot_existence_cost, COALESCE(reserved_existence.existence_cost, 0) as reserved_existence_cost, COALESCE(savings_plan_existence.existence_cost, 0)     as savings_plan_existence_cost, (COALESCE(on_demand_existence.existence_cost, 0) + COALESCE(spot_existence.existence_cost, 0) + COALESCE(reserved_existence.existence_cost, 0) +     COALESCE(savings_plan_existence.existence_cost, 0)) AS total_existence_cost
    FROM CUR
    LEFT JOIN on_demand_existence ON on_demand_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    LEFT JOIN spot_existence ON spot_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    LEFT JOIN reserved_existence ON reserved_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    LEFT JOIN savings_plan_existence ON savings_plan_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    WHERE CUR.[lineItem/ProductCode] is 'AmazonEC2' and CUR.[product/instanceType] <> "" and CUR.[lineitem/ResourceId] <> ""
    GROUP BY CUR.[lineitem/ResourceId], CUR.[product/instanceType], CUR.[product/region]
    ORDER BY total_existence_cost;
```
This first query will show results for each individual resource using the **[lineItem/ResourceId]** field. In addition to the resource ID, there are other columns that provide details on the resource and the costs for different pricing models:

- **[product/instanceType]** and **[product/region]** identify the type of instance and where the resource was used.
- There are five calculated cost columns:
- **on_demand_existence_cost** is the total cost for this resource with the on-demand pricing model.
- **spot_existence_cost** is the total cost for this resource with the spot pricing model.
- **reserved_existence_cost** is the total cost for this resource when covered by Reserved Instance reservations.
- **savings_plan_existence_cost** is the total cost for this resource when covered by Savings Plans.
- **total_existence_cost** is the sum of all pricing model costs.

Sometimes when working with large environments, showing the data for every individual resource is not helpful. This next query removes the **[lineItem/ResourceId]** field from the query results and instead groups costs by the **[product/instanceType]** and **[product/region]**.

    -Total existence cost for EC2
    --Total existence cost by instance type, region
    WITH on_demand_existence AS ( SELECT [lineItem/ResourceId], round(sum([lineItem/UnblendedCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] = 'AmazonEC2' and [    product/instanceType] <> "" and [lineItem/LineItemType] is 'Usage' and [lineItem/UsageType] LIKE '%BoxUsage%' and [lineItem/Operation] LIKE 'RunInstances%' GROUP BY [lineItem/    ResourceId]
    )
    , spot_existence AS ( SELECT [lineItem/ResourceId], round(sum([lineItem/UnblendedCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product/    instanceType] <> "" and [lineItem/LineItemType] is 'Usage' and [lineItem/UsageType] LIKE '%SpotUsage%' and [lineItem/Operation] LIKE 'RunInstances%' GROUP BY [lineItem/ResourceId]
    )
    , reserved_existence AS ( SELECT [lineItem/ResourceId], round(sum([reservation/EffectiveCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product    /instanceType] <> "" and [lineItem/LineItemType] is 'DiscountedUsage' GROUP BY [lineItem/ResourceId]
    )
    , savings_plan_existence AS ( SELECT [lineItem/ResourceId], round(sum([savingsPlan/SavingsPlanEffectiveCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] =     'AmazonEC2' and [product/instanceType] <> "" and [lineItem/LineItemType] is 'SavingsPlanCoveredUsage' GROUP BY [lineItem/ResourceId]
    )
    SELECT CUR.[product/instanceType], CUR.[product/region], COALESCE(on_demand_existence.existence_cost, 0) as on_demand_existence_cost, COALESCE(spot_existence.existence_cost, 0) as     spot_existence_cost, COALESCE(reserved_existence.existence_cost, 0) as reserved_existence_cost, COALESCE(savings_plan_existence.existence_cost, 0) as savings_plan_existence_cost, (    COALESCE(on_demand_existence.existence_cost, 0) + COALESCE(spot_existence.existence_cost, 0) + COALESCE(reserved_existence.existence_cost, 0) + COALESCE(savings_plan_    existence.existence_cost, 0)) AS total_existence_cost
    FROM CUR
    LEFT JOIN on_demand_existence ON on_demand_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    LEFT JOIN spot_existence ON spot_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    LEFT JOIN reserved_existence ON reserved_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    LEFT JOIN savings_plan_existence ON savings_plan_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    WHERE CUR.[lineItem/ProductCode] is 'AmazonEC2' and CUR.[product/instanceType] <> "" and CUR.[lineitem/ResourceId] <> ""
    GROUP BY CUR.[product/instanceType], CUR.[product/region]
    ORDER BY total_existence_cost;

## **Utilization Costs**

Now that we understand how to calculate the Existence Costs for EC2, the next section of this guide will analyze costs that are driven by the way a resource is utilized. Utilization costs are the first half of the fabled ‘EC2 - Other’ category of AWS costs.

The key takeaway about Utilization costs is these costs are driven by usage. This can cause the hourly Utilization cost of a resource to vary drastically. Below are the main categories of Utilization costs and a brief description of what these costs are:

- **Inter-Zone**: These are the costs associated with transferring data ‘In’ or ‘Out’ of EC2. Data transfer ‘In’ from the internet is free, but there are charges for data transfer ‘out’ to either the internet or other AWS Services. If you want to see the costs from AWS that show data transfer charges from EC2 to each region or product, those are posted on the [EC2 pricing page](https://aws.amazon.com/ec2/pricing/on-demand/) under Data Transfer pricing.
- **Public-IP**: These track the costs of transferring data ‘in’ or ‘out’ from a public or Elastic IPv4 address. This is charged at $0.01/GB in each direction.
- **VPC-Peering**: VPC Peering connections between the VPCs allow you to route traffic between them using private IP Addresses. All VPC Peering connections in the same AWS Region are charged at $0.01/GB.

With these three categories in mind, the query below shows all EC2 utilization costs that can be tied directly to an instance.

    -Utilization Costs
    --Utilization costs for EC2 by line item operation
    SELECT [lineItem/Operation], round(sum([lineItem/UnblendedCost]), 4) as utilization_cost
    FROM CUR
    WHERE [lineItem/ProductCode] is 'AmazonEC2' and [lineItem/ResourceId] LIKE 'i-%' and [lineItem/UsageType] NOT LIKE '%BoxUsage%' and [lineItem/Operation] NOT LIKE 'RunInstances%'     and [lineItem/LineItemType] is 'Usage'
    GROUP BY [lineItem/LineItemType], [lineItem/Operation]
    ORDER BY sum([lineItem/UnblendedCost]);

Now that we are focused on Utilization costs, the analysis will filter out different variables than when focusing on Existence costs. Basically, we want to look at costs associated with EC2 instances that are NOT Existence costs:

- **[lineItem/Operation]** is the main field that can be used to categorize Utilization costs.
- To make sure all the costs being isolated are still associated with EC2 instances, the **[lineItem/ResourceId]** is filtered to only include resource IDs that contain *i-%*, which is the nomenclature from AWS to identify EC2 instances.
- Lastly, any Existence cost needs to be filtered out of this analysis:
- **[LineItem/UsageType]** is filtered by removing *%BoxUsage%* from the calculated costs.
- **[lineItem/Operation]** is filtered by removing *RunInstances%* from the calcualted costs

With this base query, you can filter down to an individual instance, account, tag, or any other operational identifier that is helpful. The example below adds **[lineItem/UsageStartDate]** to the SELECT clause to show costs by hour and the **[lineItem/ResourceID]** to the WHERE clause to identify the Utilization costs by cost type for a single resource.

    -Utilization Costs
    --Utilization costs for EC2 by usage stard date and line item operation
    SELECT [lineItem/UsageStartDate], [lineItem/Operation], round(sum([lineItem/UnblendedCost]), 4) as utilization_cost
    FROM CUR
    WHERE [lineItem/ProductCode] is 'AmazonEC2' and [lineItem/ResourceId] is 'i-XXXXXXXXXXXXXXXX' and [lineItem/UsageType] NOT LIKE '%BoxUsage%' and [lineItem/Operation] NOT LIKE     'RunInstances%' and [lineItem/LineItemType] is 'Usage'
    GROUP BY [lineItem/UsageStartDate], [lineItem/Operation]
    ORDER BY sum([lineItem/UnblendedCost]);

Similarly, if a you wants to see the Utilization costs for all the instances in their environment, the **[lineItem/ResourceID]** field can be added in the SELECT clause:

    -Utilization Costs
    --Utilization costs for EC2 by resource ID
    SELECT [lineItem/ResourceId], [lineItem/Operation], round(sum([lineItem/UnblendedCost]), 4) as resource_utilization_cost
    FROM CUR
    WHERE [lineItem/ProductCode] is 'AmazonEC2' and [lineItem/ResourceId] LIKE 'i-%' and [lineItem/UsageType] NOT LIKE '%BoxUsage%' and [lineItem/Operation] NOT LIKE 'RunInstances%'     and [lineItem/LineItemType] is 'Usage'
    GROUP BY [lineItem/ResourceId], [lineItem/Operation]
    ORDER BY sum([lineItem/UnblendedCost]);

## **Total Resource Cost for EC2**

After finishing the analysis of Existence and Utilization costs for EC2 instances, the two costs can be combined to provide a **full resource cost** for any EC2 instance.

First, calculate the total EC2 instance cost grouped by each EC2 resource using **[lineItem/ResourceID]**. This will return a significant amount of data with a line for each EC2 instance that was active during the period being analyzed.

    -Total Resource Cost for EC2
    --Combine existence and utilization costs to get a total EC2 instance cost by EC2 resource ID.
    WITH resource_existence_cost AS (
    WITH on_demand_existence AS ( SELECT [lineItem/ResourceId], round(sum([lineItem/UnblendedCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] = 'AmazonEC2' and [    product/instanceType] <> "" and [lineItem/LineItemType] is 'Usage' and [lineItem/UsageType] LIKE '%BoxUsage%' and [lineItem/Operation] LIKE 'RunInstances%' GROUP BY [lineItem/    ResourceId]
    )
    , spot_existence AS ( SELECT [lineItem/ResourceId], round(sum([lineItem/UnblendedCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product/    instanceType] <> "" and [lineItem/LineItemType] is 'Usage' and [lineItem/UsageType] LIKE '%SpotUsage%' and [lineItem/Operation] LIKE 'RunInstances%' GROUP BY [lineItem/ResourceId]
    )
    , reserved_existence AS ( SELECT [lineItem/ResourceId], round(sum([reservation/EffectiveCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product    /instanceType] <> "" and [lineItem/LineItemType] is 'DiscountedUsage' GROUP BY [lineItem/ResourceId]
    )
    , savings_plan_existence AS ( SELECT [lineItem/ResourceId], round(sum([savingsPlan/SavingsPlanEffectiveCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] =     'AmazonEC2' and [product/instanceType] <> "" and [lineItem/LineItemType] is 'SavingsPlanCoveredUsage' GROUP BY [lineItem/ResourceId]
    )
    SELECT CUR.[lineItem/ResourceId], CUR.[product/instanceType], CUR.[product/region], COALESCE(on_demand_existence.existence_cost, 0) as on_demand_existence_cost, COALESCE(spot_    existence.existence_cost, 0) as spot_existence_cost, COALESCE(reserved_existence.existence_cost, 0) as reserved_existence_cost, COALESCE(savings_plan_existence.existence_cost, 0)     as savings_plan_existence_cost, (COALESCE(on_demand_existence.existence_cost, 0) + COALESCE(spot_existence.existence_cost, 0) + COALESCE(reserved_existence.existence_cost, 0) +     COALESCE(savings_plan_existence.existence_cost, 0)) AS total_existence_cost
    FROM CUR
    LEFT JOIN on_demand_existence ON on_demand_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    LEFT JOIN spot_existence ON spot_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    LEFT JOIN reserved_existence ON reserved_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    LEFT JOIN savings_plan_existence ON savings_plan_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    WHERE CUR.[lineItem/ProductCode] is 'AmazonEC2' and CUR.[product/instanceType] <> "" and CUR.[lineitem/ResourceId] <> ""
    GROUP BY CUR.[lineItem/ResourceId], CUR.[product/instanceType], CUR.[product/region]
    )
    , resource_utilization_cost AS (
    SELECT [lineItem/ResourceId], [lineItem/Operation], round(sum([lineItem/UnblendedCost]), 4) as total_utilization_cost
    FROM CUR
    WHERE [lineItem/ProductCode] is 'AmazonEC2' and [lineItem/ResourceId] LIKE 'i-%' and [lineItem/UsageType] NOT LIKE '%BoxUsage%' and [lineItem/Operation] NOT LIKE 'RunInstances%'     and [lineItem/LineItemType] is 'Usage'
    GROUP BY [lineItem/ResourceId], [lineItem/Operation]
    )
    SELECT CUR.[lineItem/ResourceId], CUR.[product/instanceType], CUR.[product/region], COALESCE(resource_existence_cost.total_existence_cost, 0) as total_existence_cost, COALESCE(    resource_utilization_cost.total_utilization_cost, 0) as total_utilization_cost, (COALESCE(resource_existence_cost.total_existence_cost, 0) + COALESCE(resource_utilization_cost.total    _utilization_cost, 0)) AS ec2_total_cost
    FROM CUR
    LEFT JOIN resource_existence_cost ON resource_existence_cost.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    LEFT JOIN resource_utilization_cost ON resource_utilization_cost.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    WHERE CUR.[lineItem/ProductCode] is 'AmazonEC2' and CUR.[product/instanceType] <> "" and CUR.[lineitem/ResourceId] <> ""
    GROUP BY CUR.[lineItem/ResourceId], CUR.[product/instanceType], CUR.[product/region]
    ORDER BY total_existence_cost;

Second, if the data per resource is too complex, this query will calculate the total EC2 instance cost grouped by instance type using **[product/instanceType]** and by region using **[product/region]**.

    `-Total Resource Cost for EC2
    `--Combine existence and utilization costs to get a total EC2 instance cost grouped by instance type and region.
    `WITH resource_existence_cost AS (
    `WITH on_demand_existence AS ( SELECT [lineItem/ResourceId], round(sum([lineItem/UnblendedCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] = 'AmazonEC2' and [    `product/instanceType] <> "" and [lineItem/LineItemType] is 'Usage' and [lineItem/UsageType] LIKE '%BoxUsage%' and [lineItem/Operation] LIKE 'RunInstances%' GROUP BY [lineItem/    `ResourceId]
    `)
    `, spot_existence AS ( SELECT [lineItem/ResourceId], round(sum([lineItem/UnblendedCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product/    `instanceType] <> "" and [lineItem/LineItemType] is 'Usage' and [lineItem/UsageType] LIKE '%SpotUsage%' and [lineItem/Operation] LIKE 'RunInstances%' GROUP BY [lineItem/ResourceId]
    `)
    `, reserved_existence AS ( SELECT [lineItem/ResourceId], round(sum([reservation/EffectiveCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] = 'AmazonEC2' and [product    `/instanceType] <> "" and [lineItem/LineItemType] is 'DiscountedUsage' GROUP BY [lineItem/ResourceId]
    `)
    `, savings_plan_existence AS ( SELECT [lineItem/ResourceId], round(sum([savingsPlan/SavingsPlanEffectiveCost]), 4) as existence_cost FROM CUR WHERE [lineItem/ProductCode] =     `'AmazonEC2' and [product/instanceType] <> "" and [lineItem/LineItemType] is 'SavingsPlanCoveredUsage' GROUP BY [lineItem/ResourceId]
    `)
    `SELECT CUR.[lineItem/ResourceId], CUR.[product/instanceType], CUR.[product/region], COALESCE(on_demand_existence.existence_cost, 0) as on_demand_existence_cost, COALESCE(spot_    `existence.existence_cost, 0) as spot_existence_cost, COALESCE(reserved_existence.existence_cost, 0) as reserved_existence_cost, COALESCE(savings_plan_existence.existence_cost,0) as     `savings_plan_existence_cost, (COALESCE(on_demand_existence.existence_cost, 0) + COALESCE(spot_existence.existence_cost, 0) + COALESCE(reserved_existence.existence_cost, 0) +     `COALESCE(savings_plan_existence.existence_cost, 0)) AS total_existence_cost
    `FROM CUR
    `LEFT JOIN on_demand_existence ON on_demand_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    `LEFT JOIN spot_existence ON spot_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    `LEFT JOIN reserved_existence ON reserved_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    `LEFT JOIN savings_plan_existence ON savings_plan_existence.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    `WHERE CUR.[lineItem/ProductCode] is 'AmazonEC2' and CUR.[product/instanceType] <> "" and CUR.[lineitem/ResourceId] <> ""
    `GROUP BY CUR.[lineItem/ResourceId], CUR.[product/instanceType], CUR.[product/region]
    `)
    `, resource_utilization_cost AS (
    `SELECT [lineItem/ResourceId], [lineItem/Operation], round(sum([lineItem/UnblendedCost]), 4) as total_utilization_cost
    `FROM CUR
    `WHERE [lineItem/ProductCode] is 'AmazonEC2' and [lineItem/ResourceId] LIKE 'i-%' and [lineItem/UsageType] NOT LIKE '%BoxUsage%' and [lineItem/Operation] NOT LIKE 'RunInstances%'     `and [lineItem/LineItemType] is 'Usage'
    `GROUP BY [lineItem/ResourceId], [lineItem/Operation]
    `)
    `SELECT CUR.[product/instanceType], CUR.[product/region], COALESCE(resource_existence_cost.total_existence_cost, 0) as total_existence_cost, COALESCE(resource_utilization_cost.total_    `utilization_cost, 0) as total_utilization_cost, (COALESCE(resource_existence_cost.total_existence_cost, 0) + COALESCE(resource_utilization_cost.total_utilization_cost, 0)) AS ec2_    `total_cost
    `FROM CUR
    `LEFT JOIN resource_existence_cost ON resource_existence_cost.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    `LEFT JOIN resource_utilization_cost ON resource_utilization_cost.[lineItem/ResourceId] = CUR.[lineItem/ResourceId]
    `WHERE CUR.[lineItem/ProductCode] is 'AmazonEC2' and CUR.[product/instanceType] <> "" and CUR.[lineitem/ResourceId] <> ""
    `GROUP BY CUR.[product/instanceType], CUR.[product/region]
    `ORDER BY total_existence_cost;

## **Subresource Costs**

The last component of EC2 cost analysis is also the second half of the EC2-Other category that is isolated as Subresource costs. This is usually the most confusing cost category because it focuses on resources that are not EC2 instances but are created by or directly interact with your EC2 Instances. A common example of an EC2 subresource is Elastic Block Store (EBS) volumes. EBS volumes are their own resources, with unique resource IDs, but are included in the EC2-Other cost category.

The main difference between Subresource cost analysis compared to Existence and Utilization costs is that Subresource cost analysis aggregates costs for resources that are not EC2 instances. Specifically, this analysis isolates costs for EBS Volumes, EBS Snapshots, and Nat Gateways, which all are considered part of the *AmazonEC2* product code.

    -Subresource Costs
    --Subresource costs by Resource ID
    SELECT DISTINCT [lineItem/ResourceId], round(sum([lineItem/UnblendedCost]), 4) as subresource_cost
    FROM CUR
    WHERE [lineItem/ProductCode] is 'AmazonEC2' and [lineItem/ResourceId] NOT LIKE 'i-%'
    GROUP BY [lineItem/ResourceId]
    ORDER BY sum([lineItem/UnblendedCost]);

This query for Subresource costs has two main differences from any of the queries previously in this guide:

- There is no mention of the **[lineItem/UsageType]** or **[lineItem/Operation]** fields because this query is trying to isolate the total cost of each Subresource.
- The filter for **[lineItem/ResourceID]** is specifically filtering out any results that contain *i-%*, which means the analysis is removing any EC2 Instances from the search results.
- **[lineItem/UnblendedCost]** is the cost field for all Subresources. None of the Subresources can be impacted by reservations.

With this total cost by **[lineItem/ResourceId]**, we can take it a step further and break down the analysis to isolate the different types of subresources and use **[lineItem/Operation]** to isolate the different cost types. The following three queries list all the resources and costs for the specified resource type.

### **EBS Volumes**

EBS volumes are identified by filtering the **[lineItem/ResourceID]** field for items that contain *vol-%*.

There are two main types of costs for EBS Volumes that show up in the **[lineItem/Operation]** field:

- IO Usage: read and write costs.
- CreateVolume: costs for creating provisioned EBS Volumes.
```
    -Subresource Costs
    --Subresource costs for EBS Volumes
    SELECT DISTINCT [lineItem/ResourceID], [lineItem/LineItemType], [lineItem/Operation], round(sum([lineItem/UnblendedCost]), 4) as subresource_cost
    FROM CUR
    WHERE [lineItem/ProductCode] is 'AmazonEC2' and [lineItem/ResourceId] LIKE 'vol-%'
    GROUP BY [lineItem/ResourceID], [lineitem/lineitemtype], [lineItem/Operation]
    ORDER BY sum([lineItem/UnblendedCost]);
```

### **EBS Volume Snapshots**

- EBS Volume snapshots are identified by filtering the **[lineItem/ResourceID]** field for items that contain *%snapshot%*.
- Only one main cost category for EBS Volume Snapshots shows up in the **[lineItem/Operation]** field: CreateSnapshot, which captures the cost per GB for storing snapshots.
```
    -Subresource Costs
    --Subresource costs for EBS Volume Snapshots
    SELECT DISTINCT [lineItem/ResourceID], [lineItem/LineItemType], [lineItem/Operation], round(sum([lineItem/UnblendedCost]), 4) as subresource_cost
    FROM CUR
    WHERE [lineItem/ProductCode] is 'AmazonEC2' and [lineItem/ResourceId] LIKE '%snapshot%'
    GROUP BY [lineItem/ResourceID], [lineitem/lineitemtype], [lineItem/Operation]
    ORDER BY sum([lineItem/UnblendedCost]);
```
### **NAT Gateways**

- NAT Gateways are identified by filtering the **[lineItem/ResourceID]** field for items that contain *%natgateway%*.
- There are many different cost categories for NAT Gateways, but they are largely driven by data transfer or data processing fees.

```
    -Subresource Costs
    --Subresource costs for NAT Gateways
    SELECT DISTINCT [lineItem/ResourceID], [lineItem/LineItemType], [lineItem/Operation], round(sum([lineItem/UnblendedCost]), 4) as subresource_cost
    FROM CUR
    WHERE [lineItem/ProductCode] is 'AmazonEC2' and [lineItem/ResourceId] LIKE '%natgateway%'
    GROUP BY [lineItem/ResourceID], [lineitem/lineitemtype], [lineItem/Operation]
    ORDER BY sum([lineItem/UnblendedCost]);
```