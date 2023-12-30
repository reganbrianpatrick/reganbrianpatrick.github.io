---
layout: post
title: Formula Building and Compare Functionality for AWS Pricing
description: The AWS Pricing add-on for Google Sheets is a phenomenal tool for efficiently analyzing, forecasting, and optimizing your AWS costs. Its intuitive interface seamlessly integrates current AWS pricing information into your spreadsheets, allowing you to stay up-to-date with the latest cloud cost values, and simplifying cost management for multiple members of your organization.
date: 2023-12-02 12:00:00 +0300
author: admin
image: '/images/formula-builder.jpg'
image_caption: 'Photo of AWS Pricing formula builder for Google Sheets'
tags: [Open Source, Cloud]
featured: true
---
You can use this information to compare alternative pricing models, regions, and volume reservation opportunities, including Reserved Instances in its many flavors. Best of all, since it uses standard Google Spreadsheets, collaboration between teams comes right out of the box!

If you’d like to get more information about this tool, visit our [AWS Pricing plugin](https://getstrake.com/community/aws-pricing) site.

## **The AWS Pricing Formula Builder**

One of the most helpful features of the AWS Pricing Add-On is the AWS Pricing Formula Builder. This is a sidebar you can enable within the context of your Google spreadsheet. It allows you to parameterize the AWS pricing values you want for specific cells, automatically generating the formula you require and placing it in place.

Let’s illustrate this with a simple example. Imagine you want to quickly compare the hourly cost for three types of RDS instances using reserved instances. This would be a regular part of the planning process for optimizing your workloads. You would normally start with something like this:

![Instance Type Comparison](/images/instance-comparison.jpg)

Usually, it would require you to go to the AWS pricing page for RDS or use the AWS Pricing calculator to fill those question mark cells. However, with the Pricing Formula builder, it’s as easy as selecting the appropriate parameters from the sidebar and inserting the automatically generated formula in your active cell.

![Formula Builder](/images/formula-builder-1.jpg)

You can also check the formula to be added in the sidebar as you play around with database engines, instance types, regions, and purchase types.  You can now complete your analysis within a few seconds.

![Instance Prices Calculated](/images/instance-calculated.jpg)

## **NEW: Compare functionality**

We added a useful new tool to the Formula Builder: the Compare option. It allows you to compare multiple resource dimensions at once by generating a table in a new sheet.

We will now paint a more complex scenario. You want to compare the benefits of on-demand and reservation instances for a new RDS Aurora workload, considering the possibility of multiple regions and instance types. You would traditionally collect those individual values from the Aurora pricing page or the Pricing Calculator. Although the information is available there, it could take a while to filter and find the specifics you need.

![Compare 1](/images/compare-1.jpg)

List of costs from the RDS Pricing page

And, let’s face it: most of the time you will end up playing around with values in a spreadsheet anyways. Check out how easy it is to generate a comparison table with this new plugin feature. First, go to the Compare tab in the AWS Pricing Formula Builder:

![Compare 2](/images/compare-2.jpg)

Once you select the service (in this case, RDS), you will be presented with the same filters as before: engines, instance types, region, purchase type, and reservation options. This time, however, you can select multiple values for each dimension:

![Compare 3](/images/compare-3.jpg)

In our example, we will compare Aurora MySQL from three instance types in four us-regions and consider 1- and 3-year reservations with All Upfront payments. This is what our sidebar looks like:

![Compare 4](/images/compare-4.jpg)

You are now ready to generate the comparison table with the *Insert formulas in new sheet* button. In a matter of seconds, you get a new sheet with a complete comparison table you can modify to your heart's content!

![Compare 5](/images/compare-5.jpg)

This was just one example. You can also implement comparative cost analysis for EC2, EBS, and RDS storage.

![Compare 6](/images/compare-6.jpg)

## **Learn More**

Strake is committed to maintaining the AWS Pricing add-on as an open-source project and expanding it with feedback from the open-source community. Here are some ways to stay up to date and get in touch with our team:

- If you want to learn more about the AWS Pricing project, you can get it free from the [Google Workspace Marketplace.](https://workspace.google.com/marketplace/app/aws_pricing_by_strake/378787760903)
- Join the [Strake Community](https://join.slack.com/t/strake-community/shared_invite/zt-1nisfazzn-uO5O_I28Z7N6sZ6iM2H1xA) on Slack to get in touch with our team.