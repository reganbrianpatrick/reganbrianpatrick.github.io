---
layout: post
title: Developer's Guide to AWS Costs
description: The Developer's Guide to AWS Costs is a project created and maintained by the team here at Strake. This project provides engineers with the code and insights to answer the most common AWS Cost questions using the Cost and Usage Report (CUR). The analysis is formatted as a series of 'guides' that focus on key AWS services and cost problems.
date: 2023-12-08 12:00:00 +0300
author: admin
image: '/images/dgac.jpg'
image_caption: 'The Developer`s Guide to AWS Costs'
tags: [cloud, dgac]
featured: false
---
## Introduction to AWS Costs

The [Developer's Guide to AWS Costs](https://eightlake.com/developer-guide-to-aws-costs) is a project created and maintained by the team here at Strake. This project provides engineers with the code and insights to answer the most common AWS Cost questions using the Cost and Usage Report (CUR). The analysis is formatted as a series of 'guides' that focus on key AWS services and cost problems.

AWS Billing is seriously overcomplicated. Our goal with this series of guides is to remove the confusion around AWS Cost Analysis and help engineers expedite cloud cost reduction.

If there are questions, comments, or concerns you want to see analyzed in this project, you can [add to the Roadmap](https://github.com/getstrake/developer-cost-guide/discussions/2) discussion on our GitHub page. If there is something financially sensitive you want to discuss, [reach out to us](mailto:brian@eightlake.com). We love a challenge – if there is a cost problem your team is having, we want to help solve it.

## **Background**

My name is [Brian Regan](https://www.linkedin.com/in/brianpatrickregan/) and I'm a co-founder of Strake. I started my professional career in finance and clawed my way into the product/engineering world by teaching myself Amazon Web Services.

My knowledge of corporate finance and a *basic* understanding of AWS made me an easy target to be the one to translate cloud costs into a business plan. I soon found myself being a translation service between our CFO and Engineering organization, explaining what drove our Costs of Goods Sold (COGS) and how our growth would impact the company's financial profile. I didn't know this at the time, but I was helping my company implement FinOps.

Since that first job, **I've spent thousands of hours analyzing AWS environments.** I've helped startups and multi-billion dollar publicly traded companies understand their billing. Across all the AWS customers I've worked with, the story is almost always the same: when cloud costs become an issue, analysts purchase reservations, and the CEO signs an Enterprise Discount Program (EDP) to temporarily reduce costs.

Inevitably, these temporary solutions don't solve the underlying infrastructure problems driving cost, and engineering capacity is required to understand and fix the problem. **In the worst cases, cloud cost reduction will start to compete with your product roadmap as the top Engineering priority**.

## **SQLite3**

These guides utilize SQL for basic data analysis. I chose to do this analysis using [SQLite3](https://www.sqlite.org/index.html) because it is a free, accessible tool with a gentle learning curve. Another reason I like SQLite3 is the extensive [documentation](https://www.sqlite.org/docs.html) and large online community. All cost analysis will work using SQLite3 on your terminal, but if you prefer a database editor, I recommend [SQLPro for SQLite](https://www.sqlitepro.com/), which costs $99.99/yr. [DBeaver.io](https://dbeaver.io/download/) is an excellent free alternative.

Since I am using SQLite3 in these guides and I don't make any changes to the Cost and Usage Report (CUR) before beginning the analysis, you will notice all the fields in the analysis are in brackets. Unfortunately, AWS has '/' in all of the field names in the CUR by default, which throws off queries in SQLite3.

![DGAC 1](/images/dgac-1.jpg)

## **Cost and Usage Report**

The main data source we will use for this analysis is the [AWS Cost and Usage Report (CUR)](https://docs.aws.amazon.com/cur/latest/userguide/what-is-cur.html). This is the most detailed billing report offered by AWS and the 'source of truth' for what shows up on your bill.  In the [first guide](https://eightlake.com/cost-and-usage-report-setup), I go through the setup and IAM policies required for creating this data source. There is a pretty good [data dictionary](https://docs.aws.amazon.com/cur/latest/userguide/data-dictionary.html) for the cost and usage report on the AWS documentation, but I will go into more detail about the fields used for analysis in each guide.

The CUR is free, but you will have to pay S3 charges to store the billing data (yes, AWS makes you pay to store your bill). This should be an insignificant amount of money, less than $5 per month for storing a year of billing data for most customers.

Tags used by your engineering teams will show up in the CUR if they are [activated in the billing console](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/activating-tags.html). Since tags are not activated by default and details are not populated historically, your team needs to consider which tags should be activated before you get too deep into any analysis.

Field names in the CUR contain a '/' in the names by default. This is very inconvenient for data analysis. SQLite3 won't read the field names unless the field name is in brackets. I decided that this analysis would work with the CUR out-of-the-box, so all field names in the analysis will be in brackets. For example, the field '**product/instanceType**' is noted as '**[product/instanceType]**' in the code blocks.

## **Get in Touch!**

The guides I am preparing are based on my personal experience and the situations I've worked through with Strake's customers. If you are reading this and have a specific problem your team is struggling with, check out the discussions on our [GitHub page](https://github.com/getstrake/developer-cost-guide/discussions) and submit an analysis request. Send your worst – I’m always up for a challenge!

Understanding your bill is a critical first step in reducing cloud costs, but the team at Strake is tackling much more than billing. Check out the rest of our website if you're interested in getting a product demo to see what we are building.