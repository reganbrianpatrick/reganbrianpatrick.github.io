---
layout: post
title: Cost Allocation Tagging
description: An idea we’ve pushed forward pretty consistently is that cost management in the cloud is a complex endeavor, and actions must be taken if you want to avoid surprises by the time you receive your monthly bill.
date: 2023-12-01 12:00:00
author: admin
image: '/images/cost-allocation-tagging.jpg'
image_caption: 'Cost Allocation Tagging in AWS'
tags: [Cloud]
featured: false
---

# An idea we’ve pushed forward pretty consistently is that cost management in the cloud is a complex endeavor, and actions must be taken if you want to avoid surprises by the time you receive your monthly bill.

And yes, for most people, the bill’s section in the AWS Console dashboard is the go-to resource for understanding their cloud expenses. But there is a lot more to it! And hopefully, you will considerably optimize your cost management processes after reading this article. In particular, we will explain a cost optimization method called **Cost Allocation** and present ways to implement it using **Cost Allocation Tagging**.

## **The importance of cost measurement and accountability**

To align your cloud usage and billing with your business objectives, you should start by gaining **visibility over your expenses**. But a birds-eye view of your costs might not be enough to make informed financial decisions since it’s easy to get lost in such a wide variety of services, different charge types, tiered offerings, and discounts.

This is especially important if you support multiple clients and projects inside your AWS consolidated billing. There are various options here, and no solution fits all. You might already have several accounts mapping to individual clients, projects, environments, and use cases.  It’s pretty much up to your business needs and structure.

These scenarios can get complicated. And it’s good to know that AWS already provides real-time tools for cost monitoring and insights, such as Cost Explorer or the Cost and Usage Report. But you might need more detailed reports and custom-built categories for your analysis. This is where solutions like **Cost Allocation Tagging** and **AWS Cost Categories** come into play.

## **What is cost allocation?**

Cost Allocation is all about establishing the relationship between costs from AWS spend, how those were incurred, and who or what incurred those costs. This is how you gain the granularity you need when reviewing your costs.

It allows you to look at specific items within your bills and get immediate answers to questions like:

- **Who is the owner** and has the responsibility to optimize it?
- **What workload, application, product, or environment** is causing the expenses?
- What are the spending areas experiencing the **highest growth rate**?
- What **budget optimizations** can we apply based on past trends?
- What was the **real impact of our cost optimization efforts**?

But to properly reap its benefits, you must first design a proper cost allocation strategy, which consists of a few steps:

1. Define your cost allocation boundaries.
2. Bring in your stakeholders!
3. Establish cost assignment procedures (showback/chargeback).
4. Measure with unit metrics as KPIs.
5. Handle unallocatable expenses.

Let’s take a deeper look at how you can design your strategy.

## **Best Practices for Your Cost Allocation Strategy**

### **1. Define your cost allocation boundaries**

Your cost allocation models can take different forms depending on how you organize your projects across your AWS accounts. They vary in ease of implementation and desired accuracy.

| Model | Details |
| --- | --- |
| Account Based | Straightforward and least effort-intensive. This is ideal for organizations with a well-established account structure, where each account can be treated as its cost category. |
| Business Unit or Team Based | Slightly higher level of effort in return for improved cost visibility across teams and applications. This is useful when each unit in your organization has multiple accounts for their different use cases or environments. |
| Tag Based | Provides detailed cost tracking using Cost Allocation Tags. However, it is crucial to strike the right balance between granularity and specificity according to your needs. |

Based on the level of maturity of your cost management practices, you could start by allocating costs based on your account structure, which defines a natural boundary between spend groups. Very straightforward. As your cost management requirements increase, you will see value in integrating tags and cost categories into your model, as it grants you a more accurate and business-driven abstraction on top of your regular bills.

### **2. Align reporting and monitoring with your stakeholder’s needs**

Discover the cost insights your report users need and translate them into useful reports and real-time monitoring dashboards. Requirements change depending on the requesting team (technical, accounting, compliance, or finance). You will need to use different presentations and data manipulation techniques for each.

Again, AWS provides real-time reporting tools and integrations to like Cost Explorer, Cost and Usage Reports, and AWS Organizations, among others. But you can also feed your cost data to custom-built dashboards and reports.

### **3. Establish a cost assignment procedure**

Attribute your costs to your business units, teams, clients, or organizations so you raise awareness of the cost contribution of each of them. This will also allow you to identify potential targets for cost optimizations. Two concepts are relevant to this:

**Showback**: calculate and present specific charges incurred by each relevant entity within the company. That is, answer the question: “How much did the X team spend last period?”

**Chargeback**: put your organization’s accounting processes to work and proceed with the actual charging of those attributed expenses to the relevant areas.

### **4. Measure using unit metrics as KPIs**

Say you have your AWS costs assigned to your business units or projects, and you spotted a cost increase in one. Is that a good or a bad sign? Well, that will depend on the efficiency of the business value generated by that spend. This is the goal of using **unit metrics**. They are ways to normalize your cost information and attach business relevance to it, enabling you to measure progress toward real business goals. For example, a *cost-per-ride* metric is a good starting point for a ridesharing service. If your product is a marketplace, you can use a *cost-per-purchase* metric.

Continuously decreasing unit metric values are a good thing: this implies your cost efficiency is increasing. On the other hand, an increase in your unit metrics means you are paying more per business event. Therefore your efficiency is going down: you should dig into it.

### **5. Allocate unallocatable spend**

Finally, you will inevitably find cost groups that can’t be tagged or assigned to a specific business category. These include [volume discounts due to AWS Reservations](https://eightlake.com/complete-guide-to-aws-reservations) like Reserved Instances or Savings Plans. You can use amortized costs in AWS cost reports to help you allocate these rogue items. But you might find other scenarios too. Therefore, it is relevant to define how you will manage them upfront, so you can deal with them appropriately when they present themselves.

## **Implementing Cost Allocation Tagging**

Say you have defined your cost allocation strategy to leverage **Cost Allocation Tags**. Now comes the time to put it in place. But how do you achieve this?

### **What are tags?**

First of all, let’s review what a tag is. **Tags are key-value pairs** that can be assigned to AWS resources and provide metadata about them. Most AWS resources support tags, like RDS instances, EC2 instances, VPCs, and security groups, to name a few. However, some can’t be tagged, so remember to define ways to manage those unallocatable cost items.

There are **AWS generated tags**, prefixed as aws: or with a service name. But you can also assign custom tags, called **user-defined tags**. You have relative freedom when you create and assign tags to your resources (although some [naming restrictions apply](https://docs.aws.amazon.com/tag-editor/latest/userguide/tagging.html#tag-conventions)). But to implement a proper cost allocation tagging model, there have to exist some ground rules regarding how team members start tagging resources. This is called a **Tagging Strategy**.

### **Build your Tagging Strategy**

![Tagging 1](/images/tagging-1.jpg)

To ensure you are tagging resources the right way, for a successful Cost Allocation Tagging implementation, AWS recommends following an iterative model, starting small with your highest priority, and then incrementally improving your strategy by following these steps:

- **Identify the need and use cases** within your organization. As explained before, you should always consider the level of granularity each stakeholder needs. You must cross IT boundaries for this step and integrate team members from finance, governance, or compliance.
- **Define and publish a tagging schema,** including mandatory tag keys, acceptable values, and naming conventions to ensure consistency across your AWS accounts.
- **Implement and enforce tagging practices**. You can achieve this through infrastructure as code (IaC) by standardizing tagging in your CI/CD pipelines. Additionally, you can create enforcement policies using AWS Organizations Tag Policies to define valid tag values, enforce capitalization, or report non-compliant resources. Service Control Policies, also from AWS Organizations, are another method for tag usage enforcement.
- **Measure and improve** the effectiveness of your tagging strategy using cost resource reporting tools like AWS Cost Explorer.

### **Enable tags for Cost Allocation Tagging.**

That’s right. The tags you create are not automatically used in your billing tools! This is why you must activate them to be used in cost allocation. Let’s see how this is done.

First, go to the AWS Billing Dashboard and look for the Cost allocation tags section. You can search for Billing or Cost allocation tags in the search bar.

![Tagging 2](/images/tagging-2.jpg)

Search for Billing or Cost Allocation tags.

Once inside the Cost allocation tags section, you will see a list of User-defined and AWS generated tags. These are marked as Active or Inactive depending on whether they have been enabled as cost allocation tags.

![Tagging 3](/images/tagging-3.jpg)

Cost allocation status for user-defined and AWS generated tags.

If you want to enable specific tags to be used in cost reports, select the desired tags, and mark the **Activate** option in the top right. You will get a confirmation popup. Confirm your action.

![Tagging 4](/images/tagging-4.jpg)

Confirmation for enabling a tag as a cost allocation tag.

And that’s it! You have now created new cost allocation tags. Be aware that once you’ve activated a cost allocation tag, it might take a day or two before it becomes available in your cost reports.

## **Using your Cost Allocation Tags in reports**

Cost allocation tags will appear in your standard AWS cost reporting tools: Cost Explorer, AWS Budgets, and AWS Cost and Usage Reports. You can also integrate them with custom reports using Amazon Quicksight.

In **AWS Cost Explorer,** for example, you can group or filter your expenses using the cost allocation tags you have previously enabled. It’s that simple!

![Tagging 5](/images/tagging-5.jpg)

Grouping costs using cost allocation tags.

Notice the “No tag key” value in the graph. This is a great way to detect costs incurred by untagged resources. Some might not support tags, which means they fit in the *unallocatable costs* group. As mentioned before, it is relevant to watch out for those expenses and decide how to apply the proper showback and chargeback procedures.

With **AWS Budgets**, it is just as easy. You can create a new custom cost budget from the Budgets dashboard and scope your budget based on cost allocation tag filters, similar to the AWS Cost Explorer example.

![Tagging 6]

AWS Budget example filtering by cost allocation tags

We’ve previously explained how to get the most from [AWS Cost and Usage Report](https://eightlake.com/aws-cost-and-usage-report-documentation). Cost allocation tags will also be available for said reports and are another way to handle the detailed cost information presented there.

Finally, there is another convenient feature called **AWS Cost Categories.** This service allows you to group your spending into meaningful cost categories based on multiple criteria. One of the grouping dimensions you can use is cost allocation tags, but you can also group by account, region, and service, among others.

![Tagging 7](/images/tagging-6.jpg)

Grouping by cost allocation tags in AWS Cost Categories

AWS Cost Categories allow you to create another abstraction layer on top of your cost analysis. These cost categories will also appear in AWS Cost Explorer, AWS Budgets, and AWS Cost and Usage Reports.

## **Key Takeaways**

Measuring and monitoring your AWS costs is one of the pillars of Cloud Financial Management. Within this context, **Cost Allocation** is a practice that helps optimize cost management in the cloud by establishing the relationship between costs, how they were incurred, and who incurred them. It can answer questions about ownership, cost drivers, growth areas, budget optimization, and the impact of your cost optimization efforts.

Some recommended practices to help you implement a proper cost allocation strategy are:

1. Define your cost allocation model.
2. Consider your stakeholder's needs.
3. Establish showback and chargeback procedures.
4. Measure using unit metrics.
5. Handle unallocatable expenses.

One of the models for cost allocation is **Cost Allocation Tagging**, which uses standard AWS tags to classify your cloud expenses. You first define a tagging strategy using an iterative approach, after which **you must opt-in the tags you desire to use as Cost Allocation Tags.**

After that, you can start using AWS cost reporting offerings like AWS Cost Explorer or AWS Cost Categories, which will now support tags for grouping and managing your organization's expenses.
