---
layout: post
title: A Complete Guide to AWS Reservations
description: In this overview of AWS Reservations, we will discuss the most popular reservation products like Compute Savings Plans, EC2 Savings Plans, EC2 Reserved Instances, and RDS Reserved Instances. In addition, we will mention the other reservation products offered by AWS and where you can find more information on those products.
date: 2023-12-09 12:00:00 +0300
author: admin
image: '/images/reservations.jpg'
image_caption: 'A Complete Guide to AWS Reservations'
tags: [Cloud, DGAC]
featured: false
---
In this overview of AWS Reservations, we will discuss the most popular reservation products: Compute Savings Plans, EC2 Savings Plans, EC2 Reserved Instances, and RDS Reserved Instances. In addition, we will mention the other reservation products offered by AWS and where you can find more information on those products.

## **What are AWS Reservations?**

AWS Reservations offer customers the ability to commit to using certain resources for an extended period of time in exchange for a steep discount. Discounts range from 20-75% depending on how much risk your organization is willing to take.

## **Compute Savings Plans**

[Compute Savings Plans](https://aws.amazon.com/savingsplans/compute-pricing/) are the most flexible reservation tool offered by Amazon Web Services. Using a compute savings plan from AWS, you can commit to a certain amount of 'compute' spending and receive a significant discount.

Compute Savings Plans reduce the engineering overhead of using AWS Reservations. Compute Savings Plans are automatically applied across your EC2, Fargate, and Lambda usage regardless of instance type, Region, operating system, or tenancy.

Compute Savings Plans commitments are made in dollars per hour. If you execute a Compute Savings Plan commitment of $1 for a 1-year term, that is a commitment to spending $1 on compute (either EC2, Lambda, or Fargate) at discount prices every hour for the following year.

### **Compute Savings Plan Options**

### **Term Length**

A Compute Savings Plan is either a 1-year or 3-year commitment. The longer the commitment, the more significant the discount. Committing to a three-year term will generally cause the discount rate to double.

### **Payment Options**

Payment terms for Compute Savings Plans determine whether the contract is paid upfront or hourly for the term of the agreement. The more money paid upfront will increase the discount for the Compute Savings Plan. There are three options:

1. All Upfront - The contract is paid upfront before any usage. All Upfront payment terms provide the highest discount rate.
2. Partial Upfront - Half of the contract is paid upfront, and half is paid hourly over the agreement term. Partial upfront is a lower discount rate than All Upfront but a higher discount than No Upfront.
3. No Upfront - No money paid upfront and the lowest discount rate. The cost of the contract is paid over the agreement term.

### **Compute Savings Plan Reservation Calculation**

To calculate the compute savings plan commitment your team should make, you need to understand how much for On-Demand usage is in your account. To do that, you can either use the AWS Cost Explorer or query your Cost and Usage Report. For a full walkthrough of how to query your Cost and Usage Report, check out this walkthrough on the [Developer's Guide to AWS Costs](https://getstrake.com/community/developers-guide-to-aws-costs).

Once you understand the On-Demand usage in your account, you need to calculate what that usage would cost if a Compute Savings Plan covered the usage.

For example, let's say that over the last seven days, we had 10,000 hours of m5.large On-Demand usage in the US East, North Virginia Region, needs to be covered with a three-year, All Upfront, Compute Savings plan. To calculate the Compute Savings Plan commitment to cover this usage, we must:

- Multiply the On Demand hours by the Savings Plan rate:

`Compute Savings Plan rate = $0.044  
Number of m5.large hours = 10,000
10,000 hours ** $0.044 per hour = $440*`

- Take the $440 for the last seven days, and identify the spend per hour:

`$440 for 7 days = $62.85 per day = $2.61 per hour`

Assuming your team wants to cover 100% of this usage, the hourly commitment executed in the AWS Console is **$5.71 per hour**.

## **EC2 Savings Plans**

[EC2 Savings Plans](https://aws.amazon.com/savingsplans/compute-pricing/#:~:text=Savings%20Plans%20are%20a%20flexible,1%20or%203%20year%20term.) provide a deeper discount than Compute Savings Plans but only apply to EC2 usage and require you to make a more detailed commitment. EC2 Savings Plans require AWS customers to commit to usage in a specific instance family in an AWS Region. Once this commitment is made, the savings are automatically applied to the instance families you chose, regardless of the instance type.

The additional restrictions on EC2 Savings Plans require more planning and alignment by engineering teams. Once this commitment is made, it will not automatically be applied if the team decides to change their instance family or to operate in a different region.

Similar to Compute Savings Plans, an EC2 Savings Plan commitment is made in dollars per hour.

!https://assets-global.website-files.com/62ab213c651781d4e1b9cb4d/635c0162e221d2b0fdc8e3fa_money-pile.jpg

### **EC2 Savings Plan Options**

EC2 Savings Plans require the user to select the AWS Region, Instance Family, Term Length, and Payment Option before committing.

### **AWS Region**

EC2 Savings Plans commitments are made by Region. For example, a Region could be US East (N. Virginia).

### **Instance Family**

EC2 Savings Plans require the user to select an instance family that is covered. For example, this would be 'm5' or 'c5'. It is important to note that EC2 Savings Plans are size flexible, so users can avoid committing to individual instance types.

### **Term Length**

Same as with the Compute Savings Plan, the term length options for an EC2 Savings Plan are either 1-year or 3 years. On average, going from a 1-year to a 3-year commitment with EC2 Savings Plans will increase your discount by 50%.

### **Payment Options**

Same as the Compute Savings Plan, the payment options for an EC2 Savings Plan are:

1. All Upfront - The contract is paid upfront before any usage. All Upfront payment terms provide the highest discount rate.
2. Partial Upfront - Half of the contract is paid upfront, and half is paid hourly over the agreement term. Partial upfront is a lower discount rate than All Upfront but a higher discount than No Upfront.
3. No Upfront - No money paid upfront and the lowest discount rate. The cost of the contract is paid over the agreement term.

### **EC2 Savings Plan Reservation Calculation**

The calculation for EC2 Savings Plans Reservations is very similar to Compute Savings Plans, except we don't need to worry about Fargate or Lambda usage.

Let's use the same example of 10,000 hours of m5.large On-Demand usage in the US East North Virginia Region over the past seven days. That usage needs to be covered with a three-year, All Upfront, EC2 Savings plan. To calculate the required Compute Savings Plan commitment for this usage, we must:

- Multiply the On Demand hours by the Savings Plan rate:

`Compute Savings Plan rate = $0.036 
Number of m5.large hours = 10,000
10,000 hours ** $0.036 per hour = $360*`

- Take the $360 for the last seven days, and identify the spend per hour:

`$360 for 7 days = $51.42 per day = $2.14 per hour`

Assuming your team wants to cover 100% of this usage, the reservation must be **$2.14 per hour**. The EC2 Savings Plan provides an **additional 18% discount** compared to the Compute Savings plan.

## **Reserved Instances**

Reserved Instances are the previous generation of Reservations offered by Amazon Web Services. Reserved Instances are still alive and well, but require significantly more overhead than Compute or EC2 Savings Plans.

Reserved Instances are a commitment made against resources. For example, if you are running 100 m5.large instances, you will need to commit to 100 m5.large reserved instances.

Amazon offers Reserved Instances for Elastic Compute Cloud (EC2), Relational Database Service (RDS), Elasticache, Amazon OpenSearch, Amazon Redshift, and DynamoDB. All these products require the buyer to choose a Term of 1-year or three years and Payment Options of All Upfront, Partial Upfront, or No Upfront.

### **Elastic Compute Cloud (EC2) Reserved Instances**

EC2 Reserved Instances are the most common type of Reserved Instance. EC2 Reserved Instances come in two categories: Standard and Convertible Reserved Instances.

With the creation of Compute Savings Plans and EC2 Savings Plans, it makes sense for most AWS customers to purchase those products instead of EC2 Reserved Instances. These products offer similar discounts with significantly less developer overhead.

### **Standard Reserved Instances**

Standard Reserved Instances lock in a price on instance usage and do not allow the user to change the instance family, operating system, or tenancy of the reservation. If changes need to be made to the AWS availability zone, EC2 instance size, or networking type, you can make those changes via the AWS APIs or in the AWS Management Console.

### **Convertible Reserved Instances**

Convertible Reserved Instances offer additional flexibility over Standard Reserved Instances by allowing users to change instance family, operating system, tenancy, and payment options and give customers benefits when infrastructure prices are reduced.

### **Relational Database Service (RDS) Reserved Instances**

Relational Database Service (RDS) Reserved Instances significantly discount your RDS usage. AWS customers can not cover RDS with Savings Plans products, so Reserved Instances are your only option.

Here are some of the key details of RDS Reserved Instances:

1. RDS Reserved Instances are available for all supported DB Engines and in all AWS Regions.
2. Only MySQL, MariaDB, PostgreSQL, Amazon Aurora DB Engines, and BYOL for Oracle offer instance size flexibility. There is no size flexibility for other DB engines, meaning users who have to reserve the exact instance type they plan to use.
3. Terms offered for 1 or 3 years
4. Payment Options are All Upfront, Partial Upfront, and No Upfront. The No Upfront payment option is only available for a 1-year term for RDS.

### **All Other Reserved Instances**

Amazon Web Services does offer reservation tools for other AWS products: AWS OpenSearch Service, Amazon Redshift, Amazon ElastiCache, and Amazon DynamoDB. These offerings are more complex than any of the reservations we've discussed before and require a deep understanding of the engineering roadmap before committing.

### **AWS OpenSearch Service Reserved Instances**

OpenSearch Service Reserved Instances are identical to on-demand instances. They offer a 1 or 3-year commitment with an all upfront, partial upfront, and no upfront payment option.

OpenSearch Service Reserved Instances are not size-flexible. Suppose a reservation is made for c5.large.search instances that will not cover usage for any c5.2xlarge.search instances.

For more information about OpenSearch Service Reserved Instances, check out the Reserved Instances in Amazon OpenSearch Service [AWS documentation](https://docs.aws.amazon.com/opensearch-service/latest/developerguide/ri.html).

### **Amazon Redshift Reserved Nodes**

Amazon Redshift Reserved Nodes offer the ability to reserve nodes in return for a discount. Reserved nodes come in 1 or 3-year commitments with all upfront, partial upfront, and no upfront payment options. Similar to RDS, the no upfront payment option is only available for a 1-year term.

Redshift Reserved Nodes are not size-flexible. When making a reservation, you must specify the node type you want to be covered. If this node type changes, you will pay on-demand rates for the new nodes and still be charged for your reservation.

For more information about Redshift Reserved Nodes, check the Purchasing Amazon Redshift reserved nodes [AWS documentation](https://docs.aws.amazon.com/redshift/latest/mgmt/purchase-reserved-node-instance.html).

### **Amazon ElastiCache Reserved Nodes**

Amazon ElastiCache Reserved Nodes offer customers the ability to reserve Cache node capacity for 1 or 3 years with either an all upfront, partial upfront, or no upfront pricing model. These are available for Redis and Memcached nodes and apply to all AWS Regions.

To make a reservation, you must select the Region, Term Length, and Cache Node Class, which you cannot change later. There is a maximum purchase limit of 300 cache nodes until you have to request a service level increase manually.

For more information about Amazon ElastiCache Reserved Nodes, check out the Amazon ElastiCache Reserved Nodes [AWS documentation](https://aws.amazon.com/elasticache/reserved-cache-nodes/).

### **Amazon DynamoDB Reserved Capacity**

Amazon DynamoDB Reserved Capacity offers significant savings for customers that can predict their read-and-write throughput. Users of DynamoDB Reserved Capacity pay a one-time upfront fee and a minimum usage level for the reservation duration.

DynamoDb Reserved Capacity only makes sense for AWS customers with extremely predictable DynamoDB usage or who have a minimum amount of usage they want to commit to in return for a slight reduction in spending. There is a big risk that Engineering optimizations could reduce your read-and-write throughput, and you would waste money on your reservation.

For more information on Amazon DynamoDB Reserved Capacity, check out the Amazon DynamoDB reservations [AWS documentation](https://docs.aws.amazon.com/whitepapers/latest/cost-optimization-reservation-models/amazon-dynamodb-reservations.html).