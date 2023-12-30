---
layout: post
title: AWS Pricing Formulas for EC2 and EBS
description: AWS Pricing by Strake is an open-source project that allows anyone to bring AWS Public pricing into Google Sheets using Strake's custom functions. To help the community get started with this project, Strake published several free templates to help engineers answer critical questions about AWS Pricing. Today, we will use Google Sheets templates to analyze Amazon EC2 Instance and EBS Pricing.
date: 2023-12-15 12:00:00 +0300
author: admin
image: '/images/aws-pricing-1.jpg'
image_caption: 'AWS Pricing Formulas for EC2 and EBS'
tags: [Cloud, AWS Pricing]
featured: false
---
[AWS Pricing by Strake](https://workspace.google.com/marketplace/app/aws_pricing_by_strake/378787760903) is an open-source project that allows anyone to bring AWS Public pricing into Google Sheets using Strake's custom functions. To help the community get started with this project, Strake published several free templates to help engineers answer critical questions about AWS Pricing. Today, we will use Google Sheets templates to analyze Amazon EC2 Instance and EBS Pricing.

## **Amazon Web Services Pricing is Complicated!**

Amazon Web Services operates in over **30** regions, has over **200** different services, and **thousands** of usage types. This results in a nauseating number of prices that go into calculating your monthly AWS bill.

For Amazon Elastic Compute Cloud (EC2), there are over **265,000 lines of data in the public AWS EC2 pricing file for a single region.**

On top of all the operational complexities of AWS, understanding AWS pricing and pricing changes is a critical component of understanding your AWS bill. In this blog post, we will walk through how your team can quickly analyze AWS Pricing data and use this data to your advantage when planning infrastructure changes.

## **AWS Pricing by Strake**

Strake recently launched our open-source project, [AWS Pricing by Strake](https://workspace.google.com/marketplace/app/aws_pricing_by_strake/378787760903), on Product Hunt. This project is an add-on for Google Sheets that makes up-to-date AWS public pricing available using custom Google Sheets functions.

Before we go into too much detail, here is a 3-minute video on how to get started with AWS Pricing:

‍

## **AWS EC2 Pricing**

In previous posts on Strake’s blog, including the [Developer’s Guide to AWS Costs: EC2](https://getstrake.com/blog/aws-cost-analysis-amazon-ec2-costs) and [What is EC2-Other?](https://getstrake.com/blog/what-is-ec2-other), I’ve broken down the different types of charges that come through your AWS bill under the ‘EC2’ service. Today, we will focus on understanding pricing for the hourly running cost of EC2 Instances.

### **Amazon EC2 Instance Types**

Before using AWS Pricing by Strake to calculate EC2 pricing, we must understand the concept of EC2 instance types. EC2 instance type, region, platform, and purchase type are the key variables to determine what an EC2 Instance will cost per hour.

EC2 instance types combine an EC2 instance family and the EC2 instance size. For example, the instance type “m5.large” is the combination of the instance family “m5” (anything before the ‘.’) and the instance size “large” (after the ‘.’). You can repeat this methodology for any EC2 instance type offered by AWS:

| EC2 Instance Type | EC2 Instance Family | EC2 Instance Size |
| --- | --- | --- |
| m5.large | m5 | large |
| c6i.2xlarge | c6i | 2xlarge |
| i3en.12xlarge | i3en | 12xlarge |

## **On Demand and Reserved Instance EC2 Pricing**

With the AWS Pricing by Strake project, our team has also shared several templates to help users calculate their infrastructure costs. The first template, “AWS Pricing by Strake - EC2 Pricing and Reserved Instances”, makes it simple for any user to calculate the On Demand and Reserved Instance costs for their EC2 instances.

AWS Pricing: EC2 Template

**First**, If you have not already, you can download [AWS Pricing by Strake](https://workspace.google.com/marketplace/app/aws_pricing_by_strake/378787760903) from the Google Workspace Marketplace. Downloading this add-on takes less than 15 seconds!

!https://workspace.google.com/static/img/marketplace/en/gwmBadge.svg?

**Second**, you will need to make a copy of the EC2 Template and open the sidebar to view our formula documentation. Once you have downloaded a copy of the template, click Extensions → AWS Pricing by Strake → How to use AWS Pricing.

![AWS Pricing 2](/images/aws-pricing-2.jpg)

To get started, open the sidebar documentation by going to "Extensions" --> "AWS Pricing by Strake" --> "How to use AWS Pricing"

**Third**, Once the add-on has been downloaded and the sidebar is open, you can start the use the template. Select the region, platform, and instance type of the EC2 instance you want to calculate pricing for. After the formulas calculate, you will have the On Demand and Reserved Instance prices and discount rates to analyze in Google Sheets.

![AWS Pricing 3](/images/aws-pricing-3.jpg)

You can change the Region, Platform, and Instance Type using the template.

Now that we understand how the template works, we can use this template to answer some critical business questions, for example:

**How do the prices for the new c6i instances compare to the c5a instances?** The price is often lower when AWS releases new instance families compared to the current generation. Engineers must understand the cost and operational benefits of moving to a new instance family before starting the migration.

**Should we make convertible or reserved instance reservations?** How do the discount rates compare for different payment options and commitment terms? I discussed this in another blog post, [A Complete Guide to AWS Reservations](https://strake.webflow.io/blog/a-complete-guide-to-aws-reservations). Three key variables determine how large a discount will be: length of commitment, specificity of the commitment, and the amount of money paid upfront. This EC2 template will let your business understand the different discount rates to weigh cost benefits and operational complexity.

**We're starting a new development project. What instance types should we use?** How does pricing compare across EC2 instance types? When your engineering team starts a new project, it is crucial to estimate your costs from the beginning. Maybe $0.01 per hour doesn't seem like a big difference, but when you're running thousands of instances in production, that can be a meaningful cost.

## **EC2 and EBS Volume Pricing**

The second template we have created and shared with the AWS Community, “AWS Pricing by Strake - EC2 & EBS Calculator”, combines the hourly EC2 instance cost with any attached EBS volume cost. These two costs combined give users a much better understanding of what their development project will cost.

AWS Pricing: EC2 and EBS Template

To use this template, **First**, select the details for the EC2 instances. We discussed calculating the cost for different EC2 Instance Types with the “EC2 Pricing and Reserved Instance Template”. The only difference with this template is that the user must also select the payment type and any related selections. For example, if you choose the “Purchase Type” as Reserved, you will be prompted to fill out the Offering Class, Term, and Payment Option.

![AWS Pricing 4](/images/aws-pricing-4.jpg)

Select EC2 Instance Details. If the Purchase Type is Reserved, the Offering Class, Term, and Payment Option must also be selected.

**Second**, select the details for the EBS volumes that will be attached to your instances. This step is optional. If you don’t want to calculate the cost of EBS volumes, leave those cells blank.

EBS volumes have pricing variables similar to EC2 Instances that must be chosen before AWS Pricing by Strake can reveal a price. These are Volume Type, Storage Type, Region” and Volume Size (GB).

![AWS Pricing 5](/images/aws-pricing-5.jpg)

Enter the EBS Volume details Volume Type, Storage Type, Region and Volume Size (GB).

**Third**, select the count of EC2 Instances and EBS Volumes. This value is used to calculate the total cost of your infrastructure. This cell needs to be filled. The default value for this template is “1”.

With the template filled out, the calculations will show Hourly, Daily, and Monthly total costs as well as the individual costs for EC2 and EBS. This template, in particular, is suited for when engineers are making EC2 compute or EBS storage changes and need to calculate cost changes, such as the following:

**Upgrading EBS volume types and want to understand the total cost impact.** Storage costs are just as complex as EC2 Instance Types. When your team is planning infrastructure changes, understand the cost before it happens so there are no surprises on your AWS bill.

**Expanding production infrastructure due to customer demand and need to estimate the image to their Gross Margins.** If your production environment is based on EC2 Instances with EBS volumes as the primary form of storage, this template can help you build out your Cost of Goods Sold (COGS) for your production environment. COGS is a compelling metric to track as a business to understand how your costs will scale as you win new customers.

**Calculating the total cost impact of changing infrastructure from On Demand to Reserved Instance pricing.** Total cost reduction from reservations is an important metric to track so the business understands what percentage cost reduction Reservations can actually provide. Unfortunately, Reserved Instances don't apply to EBS Volumes. This template will allow your team to combine EC2 Reserved Instances and your existing EBS volumes to calculate the total cost reduction to your infrastructure.

## **Learn More**

If you're interested in learning more about AWS Pricing by Strake, here are some additional resources:

- 🖥️ [Visit our website](https://getstrake.com/) to learn about features and download AWS Pricing templates.
- 🚀 [Create a GitHub Issue](https://github.com/getmacroscope/aws-pricing-sheets-addon/issues) for bugs or if you have ideas to improve the project.
- 💭 [Join the Strake community](https://join.slack.com/t/strake-community/shared_invite/zt-1nisfazzn-uO5O_I28Z7N6sZ6iM2H1xA) if you have questions or want to stay updated with Strake.