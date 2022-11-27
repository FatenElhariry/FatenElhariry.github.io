---
layout: post
title: AWS Cloud Practitioner Essentials
author: Faten Elhariry
categories: aws
tags: aws
---

#### What deos AWS help you in?
   **undifferentiated heavy lifting of IT**. Tasks that are common, often repetitive and ultimately time-consuming; these are the tasks AWS wants to help you with. So you can focus on what makes you unique. Over the internet, seems simple enough, but it implies that you can access those resources using a secure webpage console or programmatically. 
   - **what is cloud computing**
    On-demand delivery of IT resources and applications through the internet with pay-as-you-go pricing
  - **type of cloud computing**
    - cloud and on-premises 
    - (or private cloud) deployment models.

- **AWS Lambda** is an AWS service that lets you run code without needing to manage or provision servers. 
- **Amazon Cloud Computing**
  - it is highly flexible 
  - cost effective 
  - quick 
  - it is working on physical machine using virtualization which AWS take care of as maintenance, monitor and troubleshooting 
  <img src="/images/aws/ec2.png" />


- what is the difference between vertical scaling and horizontal scaling? 
- **What do you control in AWS service?**
  - the usage services and resources- (how many) you can do vertical scale or horizontal scale 
  - you can control the networking (subnets, CIDR, VPC) 
    - type of requests that is allowed in your service and if it public or private accessable 
- **compute as a service model**  
- **EC2 families**
  each ec2 service is catecories under a category  
   When selecting an instance type, consider the specific needs of your _workloads and applications_. This might include requirements for **compute, memory, or storage capabilities**.
   -  **General purpose instances**
    provide a balance of compute, memory, and networking resources. You can use them for a variety of workloads, such as:  
      application servers  
      gaming servers  
      backend servers for enterprise applications  
      small and medium databases  
    - **Compute optimized instances**
       are ideal for compute-bound applications that benefit from high-performance processors. Like general purpose instances, you can use compute optimized instances for workloads such as web, application, and gaming servers
      _the difference is compute optimized applications are ideal for high-performance web servers, compute-intensive applications servers, and dedicated gaming servers. You can also use compute optimized instances for batch processing workloads that require processing many transactions in a single group._
    - **Memory optimized instances**
      are designed to deliver fast performance for workloads that process large datasets in memory. In computing, memory is a temporary storage area. It holds all the data and instructions that a central processing unit (CPU) needs to be able to complete actions. Before a computer program or application is able to run, it is loaded from storage into memory. This preloading process gives the CPU direct access to the computer program.
    - **Accelerated computing instances**
      use hardware accelerators, or coprocessors, to perform some functions more efficiently than is possible in software running on CPUs. Examples of these functions include floating-point number calculations, graphics processing, and data pattern matching.
      _ hardware accelerator is a component that can expedite data processing. Accelerated computing instances are ideal for workloads such as graphics applications, game streaming, and application streaming._
    - **Storage optimized instances**
      are designed for workloads that require high, sequential read and write access to large datasets on local storage. Examples of workloads suitable for storage optimized instances include distributed file systems, data warehousing applications, and high-frequency online transaction processing (OLTP) systems.

    <img src="/images/aws/ec2_types.png" />

##### Amazon EC2 pricing
  you pay only for the compute time that you use. Amazon EC2 offers a variety of pricing options for different use cases. For example, _if your use case can withstand interruptions, you can save with Spot Instances_
  - **On-Demand Instances**
    ideal for short-term, irregular workloads that cannot be interrupted. No upfront costs or minimum contracts apply. The instances run continuously until you stop them, and you pay for only the compute time you use.
    include developing and testing applications and running applications that have unpredictable usage patterns. On-Demand Instances are not recommended for workloads that last a year or longer because these workloads can experience greater cost savings using Reserved Instances.
  - **AWS offers Savings Plans**
    enable you to reduce your compute costs by committing to a consistent amount of compute usage for a 1-year or 3-year term. This term commitment results in savings of up to 72% over On-Demand costs.
  - **Reserved Instances**
    a billing discount applied to the use of On-Demand Instances in your account. You can purchase Standard Reserved and Convertible Reserved Instances for a 1-year or 3-year term, and Scheduled Reserved Instances for a 1-year term. You realize greater cost savings with the 3-year option.
  - **Spot Instances**
    are ideal for workloads with flexible start and end times, or that can withstand interruptions. Spot Instances use unused Amazon EC2 computing capacity and offer you cost savings at up to 90% off of On-Demand prices.
    _Suppose that you have a background processing job that can start and stop as needed (such as the data processing job for a customer survey).You want to start and stop the processing job without affecting the overall operations of your business. If you make a Spot request and Amazon EC2 capacity is available, your Spot Instance launches. However, if you make a Spot request and Amazon EC2 capacity is unavailable, the request is not successful until capacity becomes available. The unavailable capacity might delay the launch of your background processing job._
  - **Dedicated Hosts**
    are physical servers with Amazon EC2 instance capacity that is fully dedicated to your use. 

