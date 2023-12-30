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

!https://assets-global.website-files.com/62ab213c651781d4e1b9cb4d/6543d740422f300857b4f639_formula-building-screen-1.png

Usually, it would require you to go to the AWS pricing page for RDS or use the AWS Pricing calculator to fill those question mark cells. However, with the Pricing Formula builder, it’s as easy as selecting the appropriate parameters from the sidebar and inserting the automatically generated formula in your active cell.

!https://assets-global.website-files.com/62ab213c651781d4e1b9cb4d/6543d758ca8bd25a67e719d3_formula-building-screen-2.png

You can also check the formula to be added in the sidebar as you play around with database engines, instance types, regions, and purchase types.  You can now complete your analysis within a few seconds.

!https://assets-global.website-files.com/62ab213c651781d4e1b9cb4d/6543d7769d31c23b34b786fd_formula-building-screen-3.png

## **NEW: Compare functionality**

We added a useful new tool to the Formula Builder: the Compare option. It allows you to compare multiple resource dimensions at once by generating a table in a new sheet.

We will now paint a more complex scenario. You want to compare the benefits of on-demand and reservation instances for a new RDS Aurora workload, considering the possibility of multiple regions and instance types. You would traditionally collect those individual values from the Aurora pricing page or the Pricing Calculator. Although the information is available there, it could take a while to filter and find the specifics you need.

!https://assets-global.website-files.com/62ab213c651781d4e1b9cb4d/6543d7923a280b98e278543d_formula-building-screen-4.png

List of costs from the RDS Pricing page

And, let’s face it: most of the time you will end up playing around with values in a spreadsheet anyways. Check out how easy it is to generate a comparison table with this new plugin feature. First, go to the Compare tab in the AWS Pricing Formula Builder:

!https://assets-global.website-files.com/62ab213c651781d4e1b9cb4d/6543d7b4f46e00883b74d5c1_formula-building-screen-5.png

Once you select the service (in this case, RDS), you will be presented with the same filters as before: engines, instance types, region, purchase type, and reservation options. This time, however, you can select multiple values for each dimension:

!https://assets-global.website-files.com/62ab213c651781d4e1b9cb4d/6543d7cca8b262ba67a54b3f_formula-building-screen-6.png

In our example, we will compare Aurora MySQL from three instance types in four us-regions and consider 1- and 3-year reservations with All Upfront payments. This is what our sidebar looks like:

!https://assets-global.website-files.com/62ab213c651781d4e1b9cb4d/6543d7e225f7df224ddfada5_formula-building-screen-7.png

You are now ready to generate the comparison table with the *Insert formulas in new sheet* button. In a matter of seconds, you get a new sheet with a complete comparison table you can modify to your heart's content!

!https://assets-global.website-files.com/62ab213c651781d4e1b9cb4d/6543d805891b7218c5b3e482_formula-building-screen-8.png

This was just one example. You can also implement comparative cost analysis for EC2, EBS, and RDS storage.

!https://assets-global.website-files.com/62ab213c651781d4e1b9cb4d/6543d822ee364f01aae3df80_formula-building-screen-9.png

## **Learn More**

Strake is committed to maintaining the AWS Pricing add-on as an open-source project and expanding it with feedback from the open-source community. Here are some ways to stay up to date and get in touch with our team:

- If you want to learn more about the AWS Pricing project, you can get it free from the [Google Workspace Marketplace.](https://workspace.google.com/marketplace/app/aws_pricing_by_strake/378787760903)
- Join the [Strake Community](https://join.slack.com/t/strake-community/shared_invite/zt-1nisfazzn-uO5O_I28Z7N6sZ6iM2H1xA) on Slack to get in touch with our team.