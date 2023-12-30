---
layout: post
title: AWS Cost and Usage Report Documentation
description: The Cost and Usage Report is the standard report from AWS for customers to understand and manage their costs. TheCost and Usage Report drives most cloud cost management tools, including AWS Cost Explorer. This documentation will outline the essential fields in the Cost and Usage Report, explain how those fields can be used for cloud cost management, and provide sample values users can use to build queries based on usage in their accounts.
date: 2023-12-10 12:00:00 +0300
author: admin
image: '/images/cur.jpg'
image_caption: 'AWS Cost and Usage Report Documentation'
tags: [Cloud]
featured: false
---
The Cost and Usage Report is the standard report from AWS for customers to understand and manage their costs. The [Cost and Usage Report](https://docs.aws.amazon.com/cur/latest/userguide/what-is-cur.html) drives most cloud cost management tools, including AWS Cost Explorer. This documentation will outline the essential fields in the Cost and Usage Report, explain how those fields can be used for cloud cost management, and provide sample values users can use to build queries based on usage in their accounts.

## **The Cost and Usage Report**

The Cost and Usage Report is the standard billing report for AWS Customers. This report is free (except for the S3 storage costs) and can be created by anyone with the proper billing permissions. For more details on the Cost and Usage Report, how to make a report, and getting started with analysis, check out [this guide](https://eightlake.com/cost-and-usage-report-setup) on the Strake blog.

This report contains over 200 fields and can be millions of records for a single month of usage. Using the documentation below, we are taking the ~200 fields in the Cost and Usage Report and filtering it down to only 30 of the most critical fields. These 30 fields will answer most of your cost management questions and greatly simplify your cloud cost management practices.

## **Cost and Usage Report Field Categories**

There are seven categories of cost fields across all Cost and Usage Reports: Bill, Identity, Pricing, Line Item, Product, Reservation, and Savings Plans. Below, we will break out the essential fields by field categories, provide details about what these fields describe, and provide sample values that will show up in your cost and usage report.

## **Bill**

Bill fields provide details about the specific bill and billing period the usage happened in. Most importantly, we need these fields to understand the period for the statement we are analyzing and the account paying the AWS bill.

### **bill/BillingPeriodStartDate**

The billing period start date will tell you the date for the beginning of the month when this usage happened. This field will help you filter down to specific months for cost analysis or can be used to identify the month when you're analyzing unusual usage patterns.

There is a complimentary field to bill/BillingPeriodStartDate called bill/BillingPeriodEndDate. Using the 'Start' date is recommended for cloud cost analysis because the date structure always has the day as '00' instead of varying based on the number of days in a month.

**Sample Values:**

    2022-03-01T00:00:00Z
    2022-06-01T00:00:00Z
    2022-07-01T00:00:00Z

### **bill/PayerAccountId**

The Payer Account ID will tell you which account is paying the bill for usage on any given line item. This field is especially valuable in cases where the account utilizing infrastructure is not the account paying for the infrastructure. An example of this could be if your organization uses AWS Organizations.

**Sample Values:**

    098765432109
    012345678901

## **Identity**

When your Cost and Usage Report gets too large, AWS will store multiple files for a single month of usage. Identity fields help users identify specific resources at specific periods across Cost and Usage report files for a single month.

### **identity/LineItemId**

The line item ID is unique for any line item in a given billing period. If you want to communicate a specific line item for cloud cost management conversations, you can easily share the Line Item ID and isolate the costs.

**Sample Values:**

    ym6omxa25tpt2on5yxvyxrbfwv5jp6nlw54sxmwfd0022558899q
    xnvfo1234567890zhlvj57j1234567890nsxwbbf3c676edlgoda

## **Pricing**

Pricing files help users understand how their charges are calculated and what drives specific cost categories.

### **pricing/unit**

The pricing unit describes the unit for lineItem/UsageAmount for every line item. For example, an EC2 instance with the Operation 'RunInstances' will show usage in hours, while data transfer costs will show usage in GB.

Users looking to understand pricing units across their usage can combine lineItem/ProductCode, lineItem/Operation, and pricing/unit to see all the possible pricing metrics.

**Sample Values:**

    Hours
    GB-month
    Request
    Terabytes
    Second
    Objects

## **Line Item**

Most cloud cost management tasks can be completed using Line Item fields in the Cost and Usage Report. These fields will cover most of the cost information and details on how resources are being utilized.

### **lineItem/AvailabilityZone**

Line Item Availability Zone is the most reliable field in the Cost and Usage Report for determining the Availability Zone hosting your usage. This field helps isolate costs in a specific availability zone when managing AWS costs.

**Sample Values:**

    eu-west-3c
    eu-west-1a
    eu-west-1
    eu-west-1b
    eu-west-1c
    us-west-2
    eu-north-1

### **lineItem/LineItemType**

Line Item Type will help break down key cost categories in your Cost and Usage Report. Four key categories of Line Item Type values are explained below in the sample values.

**Sample Values:**

    Business Values: 'Tax', 'Credits', and 'Fee'
    Normal Usage: 'Usage'
    Reserved Instances: 'DiscountedUsage', 'RIFee'
    Savings Plans: 'SavingsPlanCoveredUsage', 'SavingsPlanNegation', 'SavingsPlanRecurringFee'

### **lineItem/Operation**

The line item Operation explains the category of usage for every line item. There will likely be many operations for any AWS Service you use in your environment. The sample values below list examples of lineItem/Operation for EC2 usage.

**Sample Values:**

    RunInstances
    InterZone-In
    InterZone-Out
    PublicIP-Out
    PublicIP-In
    NatGateway

### **lineItem/ProductCode**

The Product Code will identify the AWS Service used for every line item. The fields lineItem/ProductCode and product/ProductName are very similar and can be used interchangeably for most analyses.

**Sample Values:**

    AmazonEC2
    AmazonRDS
    AWSCloudTrail
    AWSLambda

### **lineItem/ResourceID**

The Resource ID uniquely identifies resources in your AWS environment. Unfortunately, these are only available for certain AWS services, and the Resource ID has a different structure across AWS services. Below is a sample list of services covered and how those resources are identified from the AWS documentation:

**Elastic Compute Cloud Sample Values:**

    i-0095728d40ccea8c5
    i-1234567890asdfgh0

**EBS Volume Sample Values:**

    vol-09b09dfc3049057a1
    vol-abcdefgh123456789

**EBS Volume Snapshot Sample Values:**

    arn:aws:ec2:us-east-1:123456711330:snapshot/snap-1234567cf9882b4e4
    arn:aws:ec2:us-east-1:123456789012:snapshot/snap-123abcdefghijk123

**Nat Gateway Sample Values:**

    arn:aws:ec2:us-east-1:123456711330:natgateway/nat-1234567e3f38ba3b5
    arn:aws:ec2:us-west-2:123456711330:natgateway/nat-abcdefge123456789

### **lineItem/UnblendedRate**

The unblended rate for a line item is the rate at a specific point in time for your account's usage. I recommend AWS customers use the unblended rate, so you are more accurately representing costs by usage account. There is also a blended rate, which will blend rates across AWS Organizations.

**Sample Values:**

Numeric values.

### **lineItem/UsageAccountId**

The Usage Account ID identifies the account where the usage took place for each Cost and Usage Report line item. These values can be the same as bill/PayerAccountId values if the account where the usage took place is also the account paying the AWS bill.

**Sample Values:**

    123456789123
    012345678901

### **lineItem/UsageAmount**

The Usage Amount, in a specific unit, for each line item. This field is combined with lineItem/UnblendedRate to calculate the lineItem/UnblendedCost.

**Sample Values:**

Numeric values.

### **lineItem/NormalizedUsageAmount**

This field is a normalized value of the lineItem/UsageAmount field. For all instance usage in AWS, the normalized use is represented in size 'small.'

**Sample Values:**

Numeric values.

### **lineItem/UsageType**

The Usage Type will explain another level of detail when combined with the line item operation field. Instead of just showing that this line item is for an EC2 Instance running, it will explain the type of EC2 instance usage.

**Sample Values:**

    BoxUsage:m1.medium
    EU-Lambda-GB-Second
    EU-CloudFront-Out-Bytes

### **lineItem/UnblendedCost**

The Unblended Cost is a calculated field using lineItem/UnblendeRate and lineItem/UsageAmount that will show the UnblendedCost for a line item. This field can only reliably be used for on-demand and spot usage.

**Sample Values:**

Numeric values.

## **Product**

Product fields give more details about the specific resources used for this line item. Some examples of Product details we can get out of these fields include instance types, regions, and AWS product names.

### **product/instanceType**

The Instance Type field works for Amazon EC2, Amazon RDS, OpenSearch Service, Amazon Elasticache, Amazon EMR, and others.

**Sample Values:**

    t4g.xlarge
    cache.r6g.large
    ml.r5.4xlarge-Notebook

### **product/ProductName**

The AWS Product Name field will display the full product name instead of the service code.

**Sample Values:**

    Amazon Elastic Compute Cloud
    Amazon Elastic Container Service
    Amazon Simple Storage Service
    Elastic Load Balancing

### **product/region**

The region field displays the region where the usage took place for each line item. The value will show 'global' for resources not tied to a single region.

**Sample Values:**

    us-east-1
    eu-west-1
    global

### **product/fromLocation and product/toLocation**

These two fields will provide details on where the use originated from and where the destination is. These fields are beneficial for tracking data transfer costs.

**Sample Values:**

    US East (N. Virginia)
    US West (Oregon)
    Asia Pacific (Mumbai)
    EU (Ireland)
    External

## **Reservation**

Reservation fields provide details on Reserved Instances. Reserved Instances are a widely used Reservation tool in AWS, but costs can be hard to track without the proper insight. The fields below will help users understand how reservations impact the architecture costs.

### **reservation/ReservationARN**

Reserved instance reservations are all given an ARN. This ARN is tracked in the Reservation ARN field and can be used to track where specific reservations are being applied in your aws environment.

**Sample Values:**

    arn:aws:rds:us-east-1:123456789035:ri:ri-2021-06-22-33-44-40-219
    arn:aws:ec2:us-east-1:987654321330:ri:ri-2021-09-01-11-22-33-259

### **reservation/EffectiveCost**

The effective reservation cost combines two essential costs of using Reservations:

1. Amortization of upfront fees - If you pre-pay for reservations, that cost is amortized over the period of your reservation in the effective cost field.
2. Hourly Cost - If you have hourly costs for reservations, this cost is included in addition to the amortization of upfront fees.

**Sample Values:**

Numeric value.

### **reservation/UnusedNormalizedUnitQuantity**

Unused Normalized Unit Quantity calculates the normalized units (in size small) that are not used from this reservation. Analyzing this field is a simple and easy way to understand whether or not your reservations are being used.

**Sample Values:**

Numeric value.

### **reservation/EndTime**

Reservation End Time will tell your team when the reservation will expire. This field offers a straightforward way to keep your team on top of expiring reservations so they can be replaced as needed.

**Sample Values:**

    2022-06-01T21:06:54.000Z
    2022-08-01T19:28:36.000Z
    2022-09-29T19:10:59.000Z

## **Savings Plans**

Savings Plans are the new generation of reservation tools from AWS. There are significant benefits to using EC2 and Compute savings plans, but you still need to understand your Savings Plan costs and how they are being used.

### **savingsPlan/OfferingType**

There are two types of Savings plans: Compute Savings Plans and EC2 Savings Plans. This field will tell you what kind of reservation is applied to a specific line item.

**Sample Values:**

    ComputeSavingsPlans
    EC2SavingsPlans

### **savingsPlan/PaymentOption**

There are three payment options for Savings Plans: No Upfront, Partial Upfront, and All Upfront. These payment options impact your discounts and how costs flow through your billing reports.

**Sample Values:**

    No Upfront
    Partial Upfront
    All Upfront

### **savingsPlan/PurchaseTerm**

You can purchase Savings Plans for 1 year or 3 years. This field will tell you the purchase term for the savings plan applied to the line item.

**Sample Values:**

    1yr
    3yr

### **savingsPlan/SavingsPlanARN**

Similar to Reservations, Savings Plans have an ARN that can be used to track where your Savings Plan is being applied.

**Sample Values:**

    arn:aws:savingsplans::029987654321:savingsplan/439d1e6d-cb93-4145-874d-438b98765432
    arn:aws:savingsplans::0291987654321:savingsplan/1238a36-4cc8-4dd9-9cf7-937912341234

### **savingsPlan/SavingsPlanEffectiveCost**

The savings plan effective cost combines two essential costs of using Reservations:

1. Amortization of upfront fees - If you pre-pay for savings plans, that cost is automatically amortized over the period of your reservation in the effective cost field.
2. Hourly Cost - If you have hourly costs for savings plans, this cost is included in addition to the amortization of upfront fees.

**Sample Values:**

Numeric value.

### **savingsPlan/EndTime**

Savings Plan End Time will tell your team when the Savings plan will expire. This field offers a straightforward way to keep your team on top of expiring reservations so they can be replaced as needed.

**Sample Values:**

    2022-06-01T21:06:54.000Z
    2022-08-01T19:28:36.000Z
    2022-09-29T19:10:59.000Z