---
layout: post
title: What is EC2-Other?
description: The Enterprise Discount Program (EDP) is a tool for enterprise customers willing to make a long-term commitment to the AWS platform for a significant discount. EDPs differ from other AWS Reservations like Savings Plans or Reserved Instances because an EDP applies to most AWS Services.
date: 2023-12-07 12:00:00 +0300
author: admin
image: '/images/edp.jpg'
image_caption: 'Amazon Enterprise Discount Program'
tags: [Cloud]
featured: false
---
The [Enterprise Discount Program](https://aws.amazon.com/pricing/enterprise/) (EDP) is a tool for enterprise customers willing to make a long-term commitment to the AWS platform for a significant discount. EDPs differ from other AWS Reservations like Savings Plans or Reserved Instances because an EDP applies to *most* AWS Services.

In the simplest terms: customers make a financial commitment to AWS in return for a discount on most AWS Services. Once customers have entered into an EDP agreement, they are financially committed to a spending minimum regardless of their AWS usage.

The average EDP is a multi-year, multi-million dollar commitment. A customer's discount rate is calculated based on the duration of the agreement and total financial obligation. Here are two examples of how this would play out with sample EDP agreements with annual commitments and discount rates:

| Customer | Year 1 $ | Year 2 $ | Year 3 $ | Discount % |
| --- | --- | --- | --- | --- |
| Customer A | $14M | $18M | $22M | 17% |
| Customer B | $5M | $5M |  | 8% |

If there is one thing to take away from this conversation: There is **No Way Out** of an EDP Agreement. The Enterprise Discount Program (EDP) is only for the most committed AWS customers. You can, of course, always extend the agreement or commit to more money, but there is no way to get out of your base financial commitment.

## **‍How to Negotiate Your EDP Agreement**

### **Step 1: Understand the Impact on your Organization**

Negotiating an EDP agreement takes effort across different functions, from executives to individual contributors. This process can take six months if all goes well, from start to finish. Here are some examples of the work that your teams will need to tackle:

### **Engineering**

For Engineering, An EDP is a commitment to utilizing AWS services for your engineering efforts. There will need to be alignment between Engineering executives and senior engineers so that this commitment makes sense for your tech stack. For most customers, this isn't a big problem, but keep in mind that this can limit your architecture options in the future.

Finance will pressure to understand current AWS costs and for engineering to provide a forecast of future AWS costs. Since this commitment is multiple years into the future, this will require a certain amount of guesswork and must be explained and accepted across the organization.

### **Finance**

The AWS bill might be $200K monthly, but this could lead to a $10M commitment over three years. Finance will need to understand the size of the commitment, how this will impact your P&L and what this means for Gross Margins. In other words, this commitment will directly affect your fundraising timelines and hiring budget.

### **Legal**

An EDP is, first and foremost, a contract. Your legal team will need to be engaged with AWS to ensure the terms are understood, and specific terms are included in the agreement as required. This agreement can also raise questions about any partnerships or acquisition opportunities, which will require additional conversations with the executive team.

### **‍Step 2: Internal Alignment**

The first step of exploring an EDP agreement is aligning on whether this is the right choice for your business. An EDP agreement will impact the entire company, and there are some basic questions every customer should ask before engaging their AWS account rep:

1. Engineering: Are we committed to hosting our product on AWS?
2. Finance: Are we in a place to financially commit to a multi-year, multi-million dollar contract?
3. CEO: Does this make sense for our business right now?

The CEO, CFO, and CTO must be aligned before exploring an EDP agreement. If that group isn't aligned, don't move forward.

![Amazon EDP](/images/edp.jpg)

### **‍Step 3: Analysis**

Once aligned that exploring an EDP is the right choice for the business, it is time to start understanding your current AWS usage. This analysis is usually a task that falls on engineering teams:

**First**, Understand the current run rate of AWS expenses. This is a basic calculation of taking the most recent AWS bill (or Cost Explorer) and annualizing the costs. Ensure to include any AWS Marketplace spending since this will also count towards your EDP commitment.

If you're looking for more detailed cost and usage information, I recommend using the AWS Cost and Usage Report (CUR). Check out our Guide for setting up your CUR in the Developer's Guide to AWS Costs.

**Second**, estimate & subtract cost savings from your current cost profile. **Do not miss this step, or you will severely over-commit on your EDP.** There are two types of optimization your team should consider:

- Reservations: Reservation discounts are applied before your EDP commitment, so if you plan on making any reservations estimate that impact before estimating your EDP spend.
- Architectural Optimization: Assume a reduction in spending from architectural optimization. Examples include a service migration or instance family upgrades. Even though this work isn't planned, it is safe to assume some changes will happen over the next three years.

**Third**, combine steps 1 & 2 and calculate a unit cost you can use to estimate future expenses. This unit cost should be a straightforward metric that makes sense to the business, like cost per user or cost per endpoint. Take this metric and work with Finance to estimate a low, medium, and high estimate for cloud expenses over the next three years.

**Fourth**, your company should consider if there are any 'special circumstances' about your company that you want to include in your EDP agreement. Here are two examples of how these can show up in your EDP agreement:

1. Private Pricing Agreements (PPA): Specific commitments in your EDP to spend amounts for a particular service. These should be treated like RIs or Savings Plans. If your company uses substantial quantities of S3 for storage, you can commit to S3-specific spending and receive an additional discount on that usage.
2. Specific Region Commitments: If you are expanding your business to new regions, there may be an opportunity for building region-specific cost targets into your EDP.

Don't be afraid to ask if you have a unique circumstance where commitments would drive additional discounts.

With the analysis complete, the company must align on the negotiation goals with AWS. This process will look different for every organization but will most likely require additional investigation and multiple trips to executive staff meetings.

### **‍Step 4: Engage AWS**

It is time to engage your AWS Technical Account Manager (TAM) to discuss your commitment and the discount you could receive. Your initial outreach to your TAM should include your high-level goals and a timeline expectation. To streamline this process, you should designate an executive sponsor (c-suite level is best) that will have the job of leaning on AWS during the negotiation process.

It is best to have messages come from your executive sponsor, making it known that they will be the primary sponsor of this deal. During the negotiation phase, ensure a regular executive team meeting where you can get unblocked on specifics as needed.

### **‍Step 5: Sign the Deal and Track AWS Spending**

Congratulations, you have signed your EDP contract and are now stuck with AWS for multiple years! After signing the deal, make sure the discount applies to your next AWS bill. The Finance team will need to adjust how they handle billing, and someone will need to track your total spending against your commitment.

Hopefully, if you analyzed your AWS environment correctly, you won't owe AWS a massive check at the end of the year.

## **‍Additional Resources**

If your team is in the process of negotiating an EDP agreement and you'd like some support, here are a few resources to help you get started:

1. [Developer's Guide to AWS Costs](https://getstrake.com/community/developers-guide-to-aws-costs): This guide explains the cost structures of AWS services using the [AWS Cost and Usage Report (CUR)](https://getstrake.com/blog/cost-and-usage-report-setup). The first guide explains how to create and analyze the Cost and Usage Report using SQL.
2. If your team is struggling to get an alignment or analyze your usage, please [contact our team](mailto:developer-relations@getstrake.com) for a free consultation