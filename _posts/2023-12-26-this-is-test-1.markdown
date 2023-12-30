---
layout: post
title: Reserved Instances or Savings Plans
description: Cost optimization is one of the pillars for the efficient usage of AWS. However, inspecting and understanding AWS billing reports can be a challenging task. This is true whether your company embraced the cloud from the start or if you just began migrating your projects.
date: 2023-12-01 12:00:00 +0300
author: admin
image: '/images/post4.jpg'
image_caption: 'Photo for the blog post'
tags: [Cloud]
featured: true
---
Traditional IT tools for cost analysis need to be revised for the consumption-based models that govern the cloud. You are also faced with an overwhelming landscape of different service pricing models, constantly changing and adapting to new offerings and technologies.

Fortunately, AWS provides several tools and pricing models to simplify cost management and help you save money. It is essential to understand how these operate to avoid unpleasant surprises with your bill at the end of the month.

This article will compare two similar offerings: **Reserved Instances** and **Savings Plans**. Both are volume discount opportunities, but the mechanics of how a reservation is made and how those reservations impact your environment vary. Therefore, it’s crucial to acknowledge how these differences affect your budget and forecast operations before committing. By the end of this article, you will be able to understand them clearly and make the right decision for your business and technical needs.

In a previous blog post, [A Complete Guide to AWS Reservations](https://getstrake.com/blog/a-complete-guide-to-aws-reservations), we discussed both options at a high level. Feel free to visit that guide and review the details of each. This time, we will review the history of both alternatives, as this will help us understand some of their differences and recommended use cases.

## **Resource-based commitment with EC2 Reserved Instances**

Launched in 2009, **EC2** **Reserved Instances (RI)** was the first reservation tool from AWS. Here are the details for this offering:

- You can make a one or three-year commitment.
- **Commitments are resource-based**, meaning the billing discount is applied to your On-Demand instances, given they match specific characteristics defined at purchase time: EC2 instance type, platform, tenancy, and scope (regional or zonal).
- Reserved Instances discount applies to On-Demand instances only.
- For zonal commitments, AWS grants you a **capacity reservation**. This means it will keep the capacity you need available for that zone, minimizing the risk of *InsufficientInstanceCapacity* errors.
- **Standard RIs** can be sold to other AWS customers in the Reserved Instance Marketplace. AWS charges a service fee of 12% of the total upfront price of each instance you sell. You can modify your commitment's availability zone and scope (regional/zonal). You can also leverage **Instance Size Flexibility** for Linux instances, transforming instance sizes within the same family and generation.
- You can also purchase **Convertible RIs**, which can be exchanged with another convertible RI with different attributes (including instance family, type, platform, scope, or tenancy). There are specific rules for exchanging convertible RIs. In essence, you can only convert your reserved instances if the new instance's attributes are of the same or higher cost. These can not be sold in the Reserved Instance Marketplace.
- You have three payment options: **all upfront**, **partial upfront**, and **no upfront**. The higher the upfront payment, the greater the discount.
- You can gain **discounts of up to 72%** depending on the payment conditions.

This offering became a great way to reduce costs for predictable steady-cost usage of EC2 instances. The idea was quite successful, so it didn’t stop there.

## **Other Reserved Instance offerings**

A year later, in 2010, the **RDS Reserved DB Instances** model was launched. Applied to RDS instance costs only, not storage or I/O. They offer size flexibility for MySQL, MariaDB, PostgreSQL, Amazon Aurora, and BYOL Oracle instances. RDS Reserved Instances can not be transferred, sold, or canceled.

Soon other services came up with their Reserved Instance solutions:

- Amazon ElastiCache reserved nodes
- Amazon OpenSearch Service Reserved Instances
- Amazon Redshift reserved nodes
- Amazon DynamoDB reservations

These all share the same core features of EC2 Reserved Instances.

## **Pros and Cons of Reserved Instances**

The Reserved Instances model is ideal for optimizing the cost of steady-state workloads as long as you can predict and stick to specific instance types for your projects. There are a few downsides, though.

These models tie your hands in terms of flexibility, which makes them a no-go for more dynamic business requirements. Also, forecasting usage to the level of detail to include instance types can be complicated. For example, say you commit to a specific EC2 Instance type for three years, only to discover several months later that your compute needs have drastically changed.

There is some flexibility with Instance Size Flexibility, but that is still somewhat restrictive. You can also use Convertible Reserved Instances or the Reserved Instance Marketplace, but these have associated costs, either in the form of reduced discounts or operational overhead.

## **Spend-based commitment with Savings Plans**

In 2019, AWS introduced Savings Plans. This model no longer forces you to predict the usage of specific resource types. Instead, you make an **hourly spending commitment**, independent of the resources you use, which provides more flexibility and simplicity.

Savings Plans discounts apply to On Demand instances only; there’s no coverage for Spot usage or Reserved Instances.

There are currently three Savings Plans options, each supporting one or three-year term commitments.

### **Compute Savings Plans**

Discounts apply to Amazon EC2 costs, independent of instance type, size, tenancy, location, or operating system. Additionally, it includes AWS Fargate and AWS Lambda for container-based and serverless workloads, respectively. You can save up to 66% compared to On-Demand pricing. Savings are applied across all regions.

### **EC2 Instance Savings Plans**

Discounts apply to a specific instance family and region for up to 72% savings compared to On-Demand pricing. As you might have noticed, this makes it equivalent to the EC2 Reserved Instances solution, and the achievable discounts are almost identical. However, the operational overhead of the Savings Plans model is considerably lower.

### **Amazon SageMaker Savings Plans**

Discounts apply to any ML instance type, size, and region. You can save up to 64% compared to On-Demand pricing.

## **Pros and Cons of Savings Plans**

As with Reserved Instances, Savings Plans models are a practical cost optimization solution for the predictable usage of cloud resources. Furthermore, their simplicity dramatically improves **operational efficiency**, as you no longer need to make specific predictions about your infrastructure, and your discounts are automatically applied to all covered services.

Being so flexible as well, they simplify the **modernization of your infrastructure.** Since you are no longer committing to specific resource types, you can easily migrate to new AWS instance offerings in the future, even to container or serverless-based workloads in AWS Fargate or AWS Lambda.

Unlike Reserved Instances, Savings Plans do not cover AWS services for database implementations, such as ElastiCache, RDS, OpenSearch, Redshift, or DynamoDB.

Another interesting consideration is that Savings Plans can not be canceled once purchased unless you create a support ticket. And even then, you are not guaranteed to get a refund. On the other hand, Standard Reserved Instances can at least be sold in the Reserved Instance Marketplace if you no longer need them, although you will most likely have to sell them at a lower price than at the time of purchase.

## **Which one is right for you?**

Let’s summarize the differences between these two products in the following table:

| Criteria | Savings Plans | Reserved Instances |
| --- | --- | --- |
| Commitment type | Hourly spend based | Resource-based |
| Level of Discount | Up to 72% | Up to 72% |
| Payment options | One- or three-year commitments: Full, Partial to No Upfront payment | One- or three-year commitments: Full, Partial to No Upfront payment |
| Location coverage | Applies to all regions | Regional/zonal |
| Operational efficiency | Built-in simplicity | Operational overhead of selecting and converting specific instance types |
| Flexibility | Since it’s based on spending instead of resource types, supports resource modernization by default. | Flexible through Convertible RIs, at the expense of discount percentage. |
| Service Coverage | Amazon EC2, AWS Lambda, AWS Fargate, Amazon SageMaker | Amazon EC2, Amazon RDS, Amazon ElastiCache, Amazon OpenSearch, Amazon Redshift, and Amazon DynamoDB |
| Opt-out | Cannot be canceled | EC2 Standard RIs can be sold in the marketplace, by paying a 12% fee |

Considering this information, you can better evaluate your cost optimization options.

### **First steps**

First and foremost, consider your technological and business needs. You will probably consume different AWS services, so it’s good to evaluate your compute and non-compute resources separately.

Also, make sure to have right-sized your instances and auto-scaling groups. If you compare your current expenses as they are, you might be reserving more capacity than you need.

### **Savings Plans Use Cases**

For steady-state computing workloads, it’s better to go with Savings Plans. You can benefit from similar discounts, plus the simplicity and flexibility this product provides. This can be true for:

- Amazon EC2-based deployments
- Serverless solutions using AWS Lambda
- Containerized solutions using AWS Fargate
- Machine learning solutions using Amazon SageMaker

In particular, we can compare discount percentages for computing solutions. In the following table, we take a us-east-1 c5.xlarge Linux instance as an example. We make the following assumptions:

- A fixed on-demand hourly rate of USD$0.17 over the three years.
- All Upfront payments for the reservation scenarios.
- 100% utilization for the On Demand instance over the three years.

|  | 1-year cost $USD | 1-year savings % | 3-year cost $USD | 3-year savings % |
| --- | --- | --- | --- | --- |
| On Demand | $1,489.20 | – | $4,467.60 | – |
| Standard Reserved Instance | $876.00 | 41% | $1,629.00 | 64% |
| EC2 Instance Savings Plans | $876.00 | 41% | $1,629.36 | 64% |
| Convertible Reserved Instance | $1,007.00 | 32% | $1,957.00 | 56% |
| Compute Savings Plans | $1,007.40 | 32% | $1,944.72 | 56% |

As you can see, both Standard Reserved Instances and EC2 Instance Savings Plans provide nearly the same discount percentage. Similarly, the more flexible options like Convertible Reserved Instances and Compute Savings Plans offer fewer savings, being almost identical as well.

### **Reserved Instances Use Cases**

If you are consuming database services in AWS, then the Reserved Instances model will be your only available option. And that’s still the way to go for increased savings. This can be true for any solution built with RDS, ElastiCache, OpenSearch, Redshift, or DynamoDB.

Another use case for Reserved Instances is the capacity reservation feature. As discussed before, AWS attempts to guarantee the availability of your resources whenever you purchase Zonal Reserved Instances. This is useful when you need a fleet of high-end instances or if your EC2 Instances are hosted in availability zones with exhausted capacity. However, Savings Plans can also be combined with another AWS offering: the [EC2 On-Demand Capacity Reservation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-capacity-reservations.html) feature. This can help you achieve the same benefits for no additional cost.

### **The Case for On Demand**

What if you don’t plan on using your EC2 instances all the time over the commitment period? Let’s revisit our us-east-1 c5.xlarge Linux instance example. Imagine this time that you predict the instance to be used only during working hours, so your usage percentage is now close to 25%. This means that your three years On Demand cost would be:

`3-year On Demand cost = 
Full 3-year usage cost × 0.25
= USD$4,467.60 × 0.25
= USD$1,116.90`

Similarly, your 1-year usage cost would be:

`1-year On Demand cost =
Full 1-year usage cost × 0.25
= USD$1,489.20 × 0.25
= USD$372.30`

By comparing with the previous table, On Demand is the most cost effective solution for both of these terms.

### **Breakeven Point for Reservations**

Furthermore, there is a breakeven expected hourly usage after which you can safely purchase a reservation commitment. A simple way to calculate this for All upfront payments is to divide the upfront payment by the on demand hourly cost. Let’s take the 3-year Compute Savings Plan from the previous example:

`Usage hours to breakeven = 
Upfront payment / On Demand Hourly cost
= USD$1,944.72 / USD$0.17[hour]
= 11,440 hours`

This means that after 11,440 hours over the three years, your on demand costs will increase over the upfront payment. Therefore, you will incur extra expenses.

Hopefully, these scenarios help you understand how to exploit these two offerings to maximize cost reductions. You can also check comparative calculations between them in [A Complete Guide to AWS Reservations](https://getstrake.com/blog/a-complete-guide-to-aws-reservations).

## **Understanding Reserved Instances with the AWS Pricing Plugin**

You can make these or even more complex calculations using the [AWS Pricing Plugin for Google Sheets](https://getstrake.com/blog/aws-pricing-add-on-for-google-sheets), a project managed by Strake and available for free from the [Google Workspace Marketplace](https://workspace.google.com/marketplace/app/aws_pricing_by_strake/378787760903).

You can build your own spreadsheets and get real-time AWS pricing data to compare the different reservation models. In particular, feel free to use our [EC2 Pricing & Reservations](https://docs.google.com/spreadsheets/d/1GdoZ-jNQZ0IFhQPu1RMLG0eC8cS_Q4i-3aiQdrNNBNA/edit#gid=0) template as a starting point in your analysis.

## **Key Takeaways**

It is a complex task to manage costs in the cloud. But AWS offers reservation models such as Reserved Instances or Savings Plans to help you optimize your spending for steady-state workloads. Think of Reserved Instances to get discounts for predictable usage of AWS database services. Savings Plans will be a better choice for computing services due to their flexibility and simplicity. You can always use both offerings together to maximize your cost reductions.

If you don’t plan on using your EC2 instances all the time, you can calculate a breakeven point to determine whether On Demand is the best option.

The [AWS Pricing Plugin](https://getstrake.com/community/aws-pricing) provided by Strake is a great tool to help you forecast and understand your cloud costs to make the best solution for your use case.

‍