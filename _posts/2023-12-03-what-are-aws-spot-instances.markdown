---
layout: post
title: What Are AWS EC2 Spot Instances?
description: Spot instances are a purchasing model for EC2 compute instances that use spare EC2 capacity for a fraction of the On Demand price. This can bring you fantastic billing discounts, as you can save up to 90% compared to the On Demand model, depending on the conditions of your spot purchase.
date: 2023-12-03 12:00:00 +0300
author: admin
image: '/images/spot-instances.jpg'
image_caption: 'Photo for AWS Spot instances on AWS'
tags: [cloud]
featured: false
---
Sounds great, right? So why don’t we use Spot all the time? Because, of course, there is a downside: spot instances run on **unused EC2 compute capacity**, and since cloud usage constantly fluctuates over time, AWS does not guarantee enough room to keep your spot workloads running. That is, your instances could get reclaimed unilaterally by AWS at any time.

In this article, we will explore the attributes of this model, including relevant definitions, suggested use cases, a comparison with other AWS pricing models, and, finally, how to measure its benefits.

## **How do Spot Instances work?**

Unlike On-Demand instances, Spot instances must be allocated by generating a **Spot Instance Request.** There are a few recommended ways to create such a request:

- **By creating an Auto Scaling Group** with a mixed configuration of On Demand and Spot instances using the [EC2 CreateAutoScalingGroup API](https://docs.aws.amazon.com/autoscaling/ec2/APIReference/API_CreateAutoScalingGroup.html).
- **By creating an EC2 Fleet** consisting of On-Demand and Spot Instances using the [EC2 CreateFleet API](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateFleet.html).

Keep in mind that there are also other alternatives, but they are no longer supported or recommended by AWS. These are the [EC2 RunInstances API](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html), the [EC2 RequestSpotInstances API](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RequestSpotInstances.html), and the [EC2 RequestSpotFleet API](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RequestSpotFleet.html). The first two API calls are no longer being maintained, and AWS does not recommend Spot Fleets, as you are not allowed to mix instance types in a single request. Also, notice that Spot Fleets are currently presented as a legacy option in the [AWS documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-fleet.html).

Each **Spot Instance Request** consists of the following:

- The launch specification, including the desired EC2 instance type and Availability Zone, among other instance details.
- The desired number of instances.
- Your request price (either the spot price or the maximum you define).
- The request type (either one-time or persistent).
- A time range during which the request will be valid (for persistent requests).

Whenever a request is created, Amazon EC2 looks for available spare capacity. If such exists, it launches your instance, fulfilling your request. If not, it will wait until it can be fulfilled or until you cancel the request.

The launched spot instances will run until:

- You stop or terminate them yourself.
- Demand for spot instances increases, and your price no longer matches the spot price.
- AWS needs the capacity back (supply decrease)

If AWS needs to reclaim the instance, a **Spot Instance Interruption** event occurs. This happens two minutes before the actual deallocation, allowing you to react and manage the impending instance loss. Be careful, though, as AWS claims this is based on a best-effort basis, meaning that interruptions might happen before the actual notice is made available.

Interruption notices can be detected either as Amazon Eventbridge events or by querying an instance metadata endpoint. The Eventbridge event name is **EC2 Spot Instance Interruption Warning** and the source is **aws.ec2**. The instance metadata endpoint you must query is called **instance-action** and gives you the approximate time in which your instance will be terminated, stopped, or hibernated. If no notice exists yet, you will get an HTTP 404 error. You can find more details about these options in the [AWS documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-instance-termination-notices.html).

You can also use CloudTrail to find instance interruption events. They appear in the Event History with the name **BidEvictedEvent**.

![bigevicted](/images/bid-evicted.jpg)

CloudTrail Event history listing interruption events

If you created a **persistent request,** Amazon EC2 could automatically resubmit a new spot instance request. If this was a **one-time** **request**, the instance is taken down after two minutes, and AWS takes no further action.

Another helpful tool to proactively react to instance interruption is the **EC2 instance rebalance recommendation**. This event occurs whenever one of your Spot Instances is at risk of getting interrupted, so you can take preventive measures before the regular two minutes warning. By leveraging the [Amazon Eventbridge](https://aws.amazon.com/eventbridge/) notification mentioned before, you can trigger automatic actions based on real-time notifications and handle the situation according to your needs. On the other hand, service Integrations like AWS Auto Scaling or EC2 fleets allow for automatic capacity rebalancing.

Spot instances are also **integrated with other AWS services**, such as Amazon EMR (Elastic Map Reduce), Amazon ECS (Elastic Container Service), or Amazon SageMaker. We will review some example use cases below.

## **Spot Instance Market**

AWS spare capacity is grouped in various **Spot Capacity Pools**, each representing unused capacity for a unique EC2 Instance Type and Availability Zone tuple. Each pool has its **spot price**, i.e., the hourly price set by AWS and periodically adjusted based on the spot instance’s supply and demand.

The following table lists some example spot prices for a specific capacity pool: the C5 instance type in the us-east-1 availability zone.

| C5 (Linux) | On-Demand Price | Spot Price (us-east-1a) | Spot Price (us-east-1b) | Spot Price (us-east-1c) | Spot Price (us-east-1d) | Spot Price (us-east-1f) |
| --- | --- | --- | --- | --- | --- | --- |
| large | $0.085 | $0.063 | $0.057 | $0.054 | $0.058 | $0.071 |
|  | — | 25.65% | 33.06% | 36.35% | 31.76% | 16.71% |
| xlarge | $0.170 | $0.135 | $0.134 | $0.098 | $0.132 | $0.127 |
|  | — | 20.76% | 20.94% | 42.29% | 22.35% | 25.47% |
| 2xlarge | $0.340 | $0.218 | $0.233 | $0.205 | $0.230 | $0.244 |
|  | — | 35.76% | 31.41% | 39.82% | 32.50% | 28.38% |
| 4xlarge | $0.680 | $0.417 | $0.259 | $0.259 | $0.259 | $0.271 |
|  | — | 38.71% | 61.90% | 61.90% | 61.90% | 60.15% |
| 24xlarge | $4.080 | $2.478 | $2.902 | $1.555 | $1.555 | $1.555 |
|  | — | 39.27% | 28.88% | 61.90% | 61.90% | 61.90% |

These prices were taken from the Spot Instance pricing history section in the AWS Console, specifically the EC2 Dashboard. You can also generate comparison graphs and filter using your desired criteria.

![Spot Pricing History](/images/spot-pricing-history.jpg)

Comparison graph generated for the C3 family in the Spot Instance pricing history section

Notice that choosing different Availability Zones can heavily impact the discount you get using spot instances. Also, consider that these prices are referential, as they constantly change.

For a list of up-to-date spot capacity pool prices, you can check the Amazon EC2 Spot Instances Pricing page, which updates every five minutes. You can use the [AWS EC2 CLI](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-spot-price-history.html) or the Spot Requests/Pricing History section in the Amazon EC2 Console for a historical price list.

## **What about other purchasing options?**

Besides On-Demand and Spot Instances, other EC2 purchasing options also offer great discounts: Reserved Instances and Savings Plans. We’ve discussed both models in a previous blog post: [A Complete Guide to AWS Reservations](https://eightlake.com/complete-guide-to-aws-reservations).

They are volume discount options, where you commit to pay for a certain amount of compute instances/dollars for one to three years. It is important to note that both Reserved Instances and Savings Plans only apply to On-Demand usage, not Spot Instances.

Although they don’t apply to Spot Instances, they work together well, allowing considerable savings in your computing totals. Say, for example, that you keep an EC2 fleet of 180 EC2 instances simultaneously running per month. Across these instances, only 25% can be hosted in Spot Instances due to their flexibility and capacity to afford interruptions. You can maximize your savings by purchasing a Savings Plan or Reserved Instances to cover the remaining 75% of your EC2 usage.

Some valuable integrations between Spot Instances and Amazon EC2 Auto Scaling or EC2 Fleet make combining these different purchasing models easy.

If mixing them makes it difficult to analyze and manage your costs, you can head to our [Developer’s Guide to AWS Costs for EC2](https://eightlake.com/aws-ec2-cost-analysis), an invaluable resource to learn the difference between each model by analyzing your spending and selecting discounts individually.

## **Are my workloads appropriate for Spot?**

Now that you understand the inner workings of Spot Instances, a key question arises: how do I know if my use case is good enough for spot instances? As with most things in this field of work, it depends! However, we can mention two workload attributes that make for good spot Instances candidates: **fault tolerance and flexibility**.

### **Fault tolerance**

Your spot instance instances can get interrupted. There’s no way around it. Even though AWS reports the average interruption frequency to be less than 5%, this depends on the instance type you choose. You can visit the [Spot Instance Advisor](https://aws.amazon.com/ec2/spot/instance-advisor/) to get specific frequencies.

![Spot Instance Advisor](/images/spot-instance-advisor.jpg)

Example view from the Spot Instance advisor

This means that your spot workloads **must be able to support interruptions**. For example, keeping your entire production workload in spot instances is probably not the best idea. An unexpected interruption could take down essential parts of your capacity and affect your customers.

However, there is room for spot purchases in your production workloads if you use them to complement a steady On-Demand or reserved capacity. We will review this scenario shortly.

### **Flexibility**

The more Spot Capacity Pools your requests cover, the more your chances of fulfilling it. That is, flexible workloads regarding acceptable EC2 Instance Types and Availability Zones will get better results using the spot model. Suppose your use case prevents you from using a variety of EC2 instances. In that case, your requests will cover fewer spot capacity pools, which means fewer discount opportunities and recovery alternatives, should Amazon EC2 decide to claim your instances.

In this respect, AWS recommends selecting **no less than 10 EC2 Instance types** for increased coverage. Also, make sure to cover all Availability Zones inside your region.

## **Recommended Spot Instance Use Cases**

Based on these attributes, here are some excellent use cases for spot instances leveraging several AWS service integrations:

### **Web Applications (Amazon EC2 Auto Scaling Group)**

You can configure an Amazon EC2 Auto Scaling Group with a mixed instances group policy. You would use On-Demand instances to support your baseline production traffic and a combination of additional On-Demand and Spot Instances to scale out and cover unexpected usage peaks for a fraction of the cost. Under such a scenario, an interruption would only impact your additional instances, allowing you to avoid disrupting your service while gaining the discounts the spot market provides.

This scenario can be enabled during the creation of the Auto Scaling Group. In step 2: Choose instance launch options. You can configure the following:

### **Network settings**

**‍**In this case, follow the flexibility recommended practice and choose as many availability zones as you deem appropriate.

![Spot Network](/images/spot-network.jpg)

Diversify Availability Zones for your Auto Scaling Group

### **Instance type requirements**

**‍**Here it’s also recommended to be flexible and choose different EC2 Instance types based on your needs.

![Instance Type Requirements](/images/instance-type-requirements.jpg)

Leverage instance type flexibility for your Auto Scaling Group

### **Instance purchase options**

**‍**This is where you set the On Demand and Spot instances distribution for your Auto Scaling Group. You can also define a minimum number of On-Demand instances as base capacity.

![Instance Purchase Options](/images/instance-purchase-options.jpg)

Select the best On-Demand/Spot ratio for your use case

### **Allocation strategies**

**‍**When selecting spot instances in your mix, you can choose different approaches to determine where they will land. The recommended method is Price capacity optimized, considering both price and availability within your instance pools. You can also choose to utilize a capacity or a price oriented strategy. You can also enable Capacity Rebalance to take proactive actions before AWS claims back your instances.

![Allocation Strategies](/images/allocation-strategies.jpg)

Price capacity optimized is the recommended strategy for placing your spot instances

This same pattern can be applied to your Continuous Integration/Continuous Deployment solutions and lower environments, such as testing or development, where an interruption’s impact is more easily tolerated.

### **Big Data and Analytics (Amazon EMR)**

Cost can quickly get out of hand when dealing with Big Data solutions. However, this kind of workload, implying hyper-scale parallel execution of data analysis, usually supports interruptions and restarts. This is why Spot Instances integration with services like Amazon Elastic Map Reduce can give you steep savings.

You can select Spot purchases for core and task instance fleets during cluster creation. Similarly to the Auto Scaling Group integration, you can choose ratios between On-Demand and Spot, allocation strategies, and even specific behaviors whenever no spot instances are available.

![Scaling and Provisioning](/images/scaling-and-provisioning.jpg)

The same recommendations for flexibility apply to this use case: choose as many Availability Zones and instance types as possible.

### **Containerized workloads (Amazon ECS/EKS)**

Container workloads are almost always stateless and fault tolerant, a perfect match for Spot Instance purchase. You can use them across all container solutions in AWS, be it Amazon ECS or Kubernetes in EKS.

In your Kubernetes cluster, for example, you can create specific node groups using managed spot instances to host less critical applications, CI/CD workers, or other support components of your architecture.

### **Machine Learning workloads (Amazon SageMaker)**

You can also leverage the vast savings that Spot Instances provides thanks to its integration with Amazon SageMaker. Thus, you can train machine learning models using considerable computing power while paying less than the On-Demand alternative. This service's **Managed Spot Training** feature allows you to allocate specific training jobs in spot capacity pools, also defining a stopping condition, should no available instances exist.

## **Accounting for Spot savings**

The AWS Cost and Usage Report is an excellent way to review your Spot Instance costs and savings. Feel free to look at our [Developer’s Guide to AWS Costs for EC2](https://eightlake.com/aws-ec2-cost-analysis) for a comprehensive example of how to query this resource for a good understanding of your spot usage costs.

You can also access a **Savings Summary** by visiting the AWS EC2 Console inside the Spot Requests section of the dashboard. In there, you will find helpful details such as:

- Total number of spot instances
- Aggregated CPU/Memory hours and average costs
- Aggregated spot costs, including savings compared to on-demand
- A detailed list with this specific information for each spot instance

Understanding how these charges and discounts operate is essential to help you apply successful cost management strategies while integrating On-Demand, Spot, Reserved Instances, and Savings Plans.

## **Key Takeaways**

Spot Instances are a purchasing model for Amazon EC2 compute instances that offer considerable cost savings compared to On-Demand instances. However, there is a trade-off: since Spot Instances run on unused EC2 capacity, AWS does not guarantee the availability of this capacity at all times. Therefore, your spot instances can be reclaimed by AWS if there is a higher demand.

Although these interruptions don't happen frequently, and AWS provides several options to handle them proactively, fault tolerance is essential for any Spot Instances candidate. It is also a best practice to keep your choices flexible regarding EC2 Instance types and Availability Zones to minimize the probability of interruptions.

Once you decide to make a Spot purchase, several AWS service integrations will simplify your management. From this point on, it's essential to monitor the fluctuation of Spot prices, leverage other purchasing options, and analyze the costs and savings you get. This will help you implement efficient strategies for optimal cost management.