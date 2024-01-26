---
layout: post
title: What is EC2-Other?
description: EC2-Other is a cost category created by Amazon that causes a lot of heartburn for the Engineering community. If you are a heavy EC2 user, this could be your second largest cost category on your AWS Bill. This post will dive into this category to help AWS users understand what they are getting billed for and how to isolate individual cost categories within 'EC2-Other'. All the code in this blog post is included in the Developer's Guide to AWS Costs guide for EC2.
date: 2023-12-06 12:00:00 +0300
author: admin
image: '/images/ec2-other.jpg'
image_caption: 'What is EC2-Other?'
tags: [cloud]
featured: false
---
EC2-Other is a cost category created by Amazon that causes a lot of heartburn for the Engineering community. If you are a heavy EC2 user, this could be your second largest cost category on your AWS Bill. This post will dive into this category to help AWS users understand what they are getting billed for and how to isolate individual cost categories within 'EC2-Other'. All the code in this blog post is included in the [Developer's Guide to AWS Costs guide for EC2](https://eightlake.com/aws-ec2-cost-analysis).

## **Understanding EC2 Costs**

Before we can unpack what costs live in the 'EC2-Other' category, you have to understand what this category means to [total EC2 costs](https://eightlake.com/aws-ec2-cost-analysis) and how EC2 costs are calculated in various AWS reporting tools.

After researching this problem, I put together the table below for how different AWS reporting tools display total EC2 costs. Below the table, I summarize the different reporting methodologies and how these values relate. Please note that this is only true for on-demand EC2 usage.

### **Calculating Total EC2 Costs**

| AWS Bill | CUR (ServiceCode) | AWS Cost Explorer | CUR (ProductCode) |
| --- | --- | --- | --- |
| Elastic Compute Cloud | AmazonEC2 | EC2-Instances | AmazonEC2 |
| Data Transfer (partial) | AWSDataTransfer (partial) | EC2-Other | AmazonEC2 |

### **‍AWS Bill**

The AWS Bill category 'Elastic Compute Cloud' combines EC2 instance running costs, NAT Gateway, EBS, and all EC2 costs **except data transfer**. A second category in the AWS Bill called 'Data Transfer' combines data transfer costs for many different AWS Services. Unfortunately, EC2 Data Transfer costs cannot be isolated using only your AWS Bill.

### **AWS Cost Explorer**

When you search for 'EC2' under 'Service' in Cost Explorer, there are four options that come up:

- EC2-Instances (Elastic Compute Cloud - Compute)
- EC2-Other
- EC2-ELB (Elastic Load Balancing)
- EC2 Container Registry (ECR)

The first two combined, EC2-Instances and EC2-Other, make up your total EC2 costs (including data transfer for EC2). The second two, EC2-ELB and EC2 Container Registry, can be ignored because they are different services altogether.

The EC2-Instances category isolates the hourly cost of running your EC2 instances. One of the benefits of this category is that customers can quickly identify the instance costs that can be covered with reservations. EC2-Other is a calculation that combines all other costs driven by EC2 usage: EBS, NAT Gateway, EC2's portion of Data Transfer, and others.

With these two categories, EC2-Instances and EC2-Other, customers can get the total cost of running their EC2 instances. However, since this cost includes EC2's portion of data transfer costs, this will not be the same as the AWS Bill's cost for 'Elastic Compute Cloud' if there is any data transfer cost.

### **AWS Cost and Usage Report (CUR)**

The CUR is the source of truth for all AWS billing. In the Developer's Guide to AWS, I go into much more detail about the CUR, provide a data dictionary, and share instructions for setting up the CUR in your AWS Account.

For this discussion about EC2-Other costs, there are two fields that matter:

1. [lineItem/ProductCode] - the value 'AmazonEC2' captures all costs associated with running EC2 instances (including Data Transfer).
2. [product/ServiceCode] - the valued 'AmazonEC2' will show all EC2 costs other than Data Transfer, and 'AWSDataTransfer' is used for data transfer costs by any AWS Service.

### **Data Sources Key Takeaways**

There are three key takeaways from the different AWS data sources before we dig into the cost categories that make up EC2-Other.

**First:** EC2-Other is a calculated metric from Cost explorer. This metric calculates the total cost of running your EC2 instances, less the fixed hourly instance running costs.

**Second:** If you want to dig into costs for categories on your AWS Bill, the top cost categories on the AWS Bill align to the CUR's [product/ServiceCode] fields:

| AWS Bill Categories | [product/ServiceCode] Values |
| --- | --- |
| Elastic Compute Cloud | AmazonEC2 |
| Data Transfer | AWSDataTransfer |

**Third:** The sum of EC2-Instances and EC2-Other from cost explorer:**‍**

- **IS** equal to [lineItem/ProductCode] is 'AmazonEC2'**‍**
- **IS NOT** equal to 'Elastic Compute Cloud' on the AWS Bill because this field does not include EC2's portion of Data Transfer costs.

## **Visibility into EC2-Other Costs**

In the [Developer's Guide to AWS Costs](https://eightlake.com/developer-guide-to-aws-costs), the first service I posted a guide for was EC2. In this guide, I broke EC2 costs into three logical categories:

1. **[Existence Cost](https://eightlake.com/aws-ec2-cost-analysis)** - *EC2-Instances* in cost explorer - This identifies the cost of having an EC2 instance running regardless of the resource activity. This cost is based on an hourly rate determined by your instance type, region, and whether you're using contractual reservations or spot instances.
2. **[Utilization Cost](https://eightlake.com/aws-ec2-cost-analysis)** - *part of* *EC2 - Other* in cost explorer - On top of the flat rate for having an instance running, some costs come from how an EC2 instance is utilized. These costs are driven by a usage-based metric such as $ per GB-month. The most common Utilization cost for EC2 is data transfer.
3. **[Subresource Cost](https://eightlake.com/aws-ec2-cost-analysis)** - *the second part of* *EC2 - Other* in cost explorer - This is incurred for resources attached to or directly interacting with your EC2 instance. An easy example of EC2 is EBS Volumes used for EC2 instance storage.

I broke down the 'EC2-Other' category from Cost Explorer into Utilization and Subresource costs. Utilization focuses on how your EC2 resources are used, while Subresources isolates other resources (NAT Gateways or EBS Volumes, for example) that contribute to your total EC2 cost.

## **Total EC2-Other Costs**

### **Utilization Costs**

To isolate [Utilization Costs](https://eightlake.com/aws-ec2-cost-analysis) by Operation, use the query below. This query isolates your EC2 instances using the **[lineItem/ResourceId]** field and filters out types **[lineItem/UsageType]** and **[lineItem/Operation]** fields that indicate Existence cost.

    -Utilization Costs
    --Utilization costs for EC2 by line item operation
    SELECT	[lineItem/Operation],	round(sum([lineItem/UnblendedCost]), 4) as utilization_cost
    FROM CUR
    WHERE	[lineItem/ProductCode] is 'AmazonEC2'	and [lineItem/ResourceId] LIKE 'i-%'	and [lineItem/UsageType] NOT LIKE     '%BoxUsage%'	and [lineItem/Operation] NOT LIKE 'RunInstances%'	and [lineItem/LineItemType] is 'Usage'
    GROUP BY	[lineItem/Operation]
    ORDER BY	sum([lineItem/UnblendedCost]);`

Some of the sample values from **[lineItem/Operation]** that indicate Utilization costs include:

- VPCPeering-In and VPCPeering-Out - VPC Peering connections between the VPCs allow you to route traffic using private IP Addresses. All VPC Peering connections in the same AWS Region are charged at **$0.01/GB**.
- PublicIP-In and PublicIP-Out - These track the costs of transferring data 'in' or 'out' from a public or Elastic IPv4 address. This traffic is charged at **$0.01/GB** in each direction.
- InterZone-In and InterZone-Out - These are the costs associated with transferring data 'In' or 'Out' of EC2. Data transfer 'In' from the internet is free, but there are charges for data transfer 'out' to either the internet or other AWS Services. If you want to see the costs from AWS that show data transfer costs from EC2 to each region or product, those are posted on the [EC2 pricing page](https://aws.amazon.com/ec2/pricing/on-demand/) under Data Transfer.

If you add the **[product/ServiceCode]** field to the SELECT statement of this query, you will be able to see which costs are categorized as 'AWSDataTransfer' and which are 'AmazonEC2'.

    -Subresource Costs
    --Subresource costs for EBS Volumes
    SELECT DISTINCT	[lineItem/ResourceID],	[lineItem/LineItemType],	[lineItem/Operation],	round(sum([lineItem/    UnblendedCost]), 4) as subresource_cost
    FROM CUR
    WHERE	[lineItem/ProductCode] is 'AmazonEC2'	and [lineItem/ResourceId] LIKE 'vol-%'
    GROUP BY	[lineItem/ResourceID],	[lineitem/lineitemtype],	[lineItem/Operation]
    ORDER BY	sum([lineItem/UnblendedCost]);`

## **‍Subresource Cost**

There are three main categories of [Subresources](https://eightlake.com/aws-ec2-cost-analysis) customers should have the ability to analyze in more detail:

### **EBS Volumes**

- [EBS volumes](https://eightlake.com/aws-ec2-cost-analysis) are identified by filtering the **[lineItem/ResourceID]** field for items that contain *vol-%*.
- There are two main types of costs for EBS Volumes that show up in the **[lineItem/Operation]** field: IO Usage: read and write costs and CreateVolume: costs for creating provisioned EBS Volumes.
- `-Subresource Costs
--Subresource costs for EBS Volumes
SELECT DISTINCT	[lineItem/ResourceID],	[lineItem/LineItemType],	[lineItem/Operation],	round(sum([lineItem/UnblendedCost]), 4) as subresource_cost
FROM CUR
WHERE	[lineItem/ProductCode] is 'AmazonEC2'	and [lineItem/ResourceId] LIKE 'vol-%'
GROUP BY	[lineItem/ResourceID],	[lineitem/lineitemtype],	[lineItem/Operation]
ORDER BY	sum([lineItem/UnblendedCost]);`

### **‍EBS Volume Snapshots**

- [EBS Volume Snapshots](https://eightlake.com/aws-ec2-cost-analysis) are identified by filtering the **[lineItem/ResourceID]** field for items that contain *%snapshot%*.
- Only one main cost category for EBS Volume Snapshots shows up in the **[lineItem/Operation]** field: CreateSnapshot, which captures the cost per GB for storing snapshots.
- `-Subresource Costs
--Subresource costs for EBS Volume Snapshots
SELECT DISTINCT	[lineItem/ResourceID],	[lineItem/LineItemType],	[lineItem/Operation],	round(sum([lineItem/UnblendedCost]), 4) as subresource_cost
FROM CUR
WHERE	[lineItem/ProductCode] is 'AmazonEC2'	and [lineItem/ResourceId] LIKE '%snapshot%'
GROUP BY	[lineItem/ResourceID],	[lineitem/lineitemtype],	[lineItem/Operation]
ORDER BY	sum([lineItem/UnblendedCost]);`

### **‍NAT Gateways**

- [NAT Gateways](https://eightlake.com/aws-ec2-cost-analysis) are identified by filtering the **[lineItem/ResourceID]** field for items that contain *%natgateway%*.
- There are many different cost categories for NAT Gateways, but they are primarily driven by data transfer or data processing fees.
- `-Subresource Costs
--Subresource costs for NAT Gateways
SELECT DISTINCT	[lineItem/ResourceID],	[lineItem/LineItemType],	[lineItem/Operation],	round(sum([lineItem/UnblendedCost]), 4) as subresource_cost
FROM CUR
WHERE	[lineItem/ProductCode] is 'AmazonEC2'	and [lineItem/ResourceId] LIKE '%natgateway%'
GROUP BY	[lineItem/ResourceID],	[lineitem/lineitemtype],	[lineItem/Operation]
ORDER BY	sum([lineItem/UnblendedCost]);`

## **Further Reading**

Thank you for reading! If you are looking for more information on EC2 Costs, you can read the full [EC2 Cost Guide](https://eightlake.com/aws-ec2-cost-analysis) on the [Developer's Guide to AWS Costs](https://eightlake.com/developer-guide-to-aws-costs). If your team is working to understand your AWS costs, here are some additional resources for you:

1. A [Cost and Usage Report Setup Guide](https://eightlake.com/cost-and-usage-report-setup) including IAM Permissions
2. [Relational Database Service Cost Guide](https://eightlake.com/aws-rds-cost-analysis) to understand your RDS costs
3. To get in touch with our team with additional questions, [email us](mailto:brian@eightlake.com) or leave us a message on our [GitHub Discussions page](https://github.com/getstrake/developer-cost-guide/discussions)!