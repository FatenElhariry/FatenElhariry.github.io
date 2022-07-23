---
layout: post
title: WAS Basics!
author: Faten Elhariry
categories: Devops
tags: AWS Devops
---

#### What is Service as a Function? (serverless)
  - it is like AWS Lambda where it is a compute service that lets you run code without provisioning or managing servers.
  - it allow to developer to be focuse only on writting code and all related task will be handle by the service provider memory managment, cpu, networking and logs 
  - there are set of predefined languages that allow you to write code.
  - you only pay for the computation time only when your code is being executed.
#### Simple Queue Service (SQS) 
  - is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications.
  - eliminates the complexity and overhead associated with managing and operating message-oriented middleware, and empowers developers to focus on differentiating work 
#### what is the benefits of using Cloud?
  - cost: There is no up-front cost and resources can be precisely metered to meet demand.
  - Speed: it is provide high avaliablity and service managment 
  - Global Scale: can be provisioned all over the world to meet demand in a geographic region
  - **Productivity**: Many tasks, such as racking servers, configuring network hardware, and physically securing a data center, no longer exists. Companies can focus on building core intellectual property versus reinventing the wheel.
  - Performance: The performance of applications can leverage a continuous upgrade cycle. The network, the storage and the compute improve over time consistently. Additionally, Cloud-Native applications are able to leverage new capabilities in the cloud to further increase performance.
  - Reliability: The core architecture of the cloud offers redundancy at every step. There are multiple regions and multiple data centers in each region. Cloud-native architecture can design around these capabilities, leading to highly available architectures. Additionally, many-core cloud services are themselves highly available, like Amazon S3, which has nine nines, or 99.999999999% reliability.
  - Security: You are only as good as your weakest link with security. By consolidating to centralized security, a higher level of security occurs. Problems such as physical access to a data center or encryption at rest become industry standard on day one.


#### IAC (infrastructure as code) 
  to fully define the infrastructure. This means that deployment can target a dynamically created environment and that software can be automatically deployed to a new environment as it is created. Humans again are eliminated from the process of deployment increasing quality.


#### DevOps
  Cloud-native systems utilize DevOps. DevOps is a combination of automation, processes, and tools that increase automation, collaboration, and operational efficiency. Using Python for DevOps is a common way this automation is achieved.

#### Agility
  The agility of developing solutions improves. The cloud speeds up development time and increases quality through the use of IaC (Infrastructure as Code) and Continuous Delivery.

#### What is meaning of serverless in terms of cloud?
  it means that the developer no longer care about the serve he only care about writing code and what differentiate your business among other competitors `Allows you to write and deploy code without having to worry about the underlying infrastructure.`

**Cloud Computing** 
is the delivery of IT resources over the Internet. The cloud is like a virtual data center accessible via the Internet that allows you to manage:

- Storage services like databases
- Servers, compute power, networking
- Analytics, artificial intelligence, augmented reality
- Security services for data and applications

#### Type of cloud services 
- infrastructure as a service
- Platform as a service
- software as a service 

#### Cloud computing Models 
- Public Cloud 
- Private Cloud
- Hybrid Cloud 

#### Application integration
- Simple Queue Service (SQS)
- Simple Notification Service (SNS)

#### Global Infrastructure
##### Region
  A region is considered a geographic location or an area on a map.
##### Availability Zone
  An availability zone is an isolated location within a geographic region and is a physical data center within a specific region.
##### Edge Location
  An edge location is as a mini-data center used solely to cache large data files closer to a user's location.


#### Shared Responsibility Model
  AWS is responsible for security OF the cloud, we are responsible for security IN the cloud.
  - cloud: ensure the security of the tools 
  - you: ensure the security of the system

#### Elastic Block Store
  Elastic Block Store (EBS) is a storage solution for EC2 instances and is a physical hard drive that is attached to the EC2 instance to increase storage.
  - the data stored in EBS will be avaliable even if we terminated the machine 
  - automatically replicated within the avaliablity zone 

#### Flat Network 
  - shared among variety AWS users 
  - is the simplest and that reduces cost and the overall administration
  - all devices and workstations are connected to single switch where all devices are the in the same broadcost networking and communicate with each other  

#### Virtual Private Cloud (VPC)
  Virtual Private Cloud or VPC allows you 
  - to create your own private network in the cloud. 
  - You can launch services, like EC2, inside of that private network. 
  - A VPC spans all the Availability Zones in the region.
  - VPC allows you to control your virtual networking environment, which includes:
    - IP address ranges
    - subnets
    - route tables
    - network gateways
  - The default limit is 5 VPCs per Region. You can request an increase for these limits.
  - Your AWS resources are automatically provisioned in a default VPC.
  - There are no additional charges for creating and using the VPC.
  - You can store data in Amazon S3 and restrict access so that it’s only accessible from instances in your VPC.
  - is an isolated virtual-network dedicated to your AWS resources, such as Amazon EC2 instances.
#### network access control list (ACL)
  defines the set of firewall rules for controlling traffic coming in and out of subnets in your VPC.

#### Compute Power In The Cloud
  Compute power in the cloud is a faster way to build applications, providing:
  - no servers to manage (i.e. serverless)
  - ability to continuously scale
  - ability to run code on demand in response to events
  - pay only when your code runs

#### Lambda (it is a serverless and compute power Environment)
  AWS Lambda provides you with computing power in the cloud by allowing you to execute code without standing up or managing servers.
  - Lambda is found under the Compute section on the AWS Management Console.
  - Lambdas have a time limit of 15 minutes.
  - The code you run on AWS Lambda is called a “Lambda function.”
  - Lambda code can be triggered by other AWS services.
  - AWS Lambda supports Java, Go, PowerShell, Node.js, C#/.NET, Python, and Ruby. There is a Runtime API that allows you to use other programming languages to author your functions.
  - Lambda code can be authored via the console.
  - Lambda provides a Designer user interface that allows choosing other AWS services, as event sources, to trigger your code, and use a destination (optional) of your choice to store the generated/processes output.
  - we have chosen the default option **Created a new role with basic Lambda permissions**. In this case, the Lambda will create an execution role with permission to **upload logs to Amazon CloudWatch Logs**.

#### CDN (CloudFront)
  - CloudFront is found under the Networking & Content Delivery section on the AWS Management Console.
  - Amazon countinously adds new Edge Locations.
  - CloudFront ensures that end-user requests are served from the closest edge location.
  - CloudFront works with non-AWS origin sources.
  - You can use GeoIP blocking to serve content (or not serve content) to specific countries.
  - Cache control headers determine how frequently CloudFront needs to check the origin for an updated version your file.
  - The maximum size of a single file that can be delivered through Amazon CloudFront is 20 GB.

#### AWS WAF (or AWS Web Application Firewall) 
  provides a firewall that protects your web applications. WAF can stop common web attacks by reviewing the data being sent to your application and stopping well-known attacks.
  - monitoring and controlling the web requests coming to an Amazon API Gateway API, an Amazon CloudFront distribution, or an Application Load Balancer.
  - it is consist of 
    - **AWS WAF**
      It allows you to protect your web applications from common web exploits by monitoring and controlling the web requests coming to an Amazon API Gateway API, an Amazon CloudFront distribution, or an Application Load Balancer. 
    - **AWS Shield** 
      It provides continuous DDoS attack detection and automatic mitigations. AWS Shield offers two tiers of protection - Standard and Advanced.
    - **AWS Firewall Manager** 
      It allows you to configure and manage firewall rules across accounts and applications centrally.

Within AWS WAF service, you can create Web access control lists (web ACLs) to monitor HTTP(S) requests for AWS resources. You can protect the following types of resources:
  - CloudFront distributions
  - Regional resources (Application Load Balancer, API Gateway, AWS AppSync)

#### Identity & Access Management (IAM)
  Identity & Access Management (IAM) is an AWS service that allows us to configure who can access our AWS account, services, or even applications running in our account. IAM is a global service and is automatically available across ALL regions.

  service securely controls access to AWS resources by authenticating and authorizing (giving granular permissions) the individual users, applications, or services. Let's watch a demo next.

1. IAM User
  A user is a unique identifier generated by the IAM service and recognized by all AWS services to grant access to AWS resources. A user can be a person, system, or application that requires access to AWS services. You can generate login credentials and access keys for any user in your account. Roles and policies control the scope (permissions) of a user's access to AWS resources in your account.

2. IAM Group
  A group collects IAM users with the same level of permissions to access AWS resources. You can attach or detach permissions to a group using access control policies. A group makes it easier to manage IAM users with the same level of permissions.

3. IAM Role
  A role is simply a set of policies (permissions) to access AWS services. You can assign a role either to an IAM user or an AWS service such as EC2. Creating and storing roles helps to delegate access with defined permissions without sharing long-term access keys.

4. IAM Policy
  An access control policy is a JSON file that defines the resource to grant access, level of access, and allowed actions. You can attach a policy to multiple users, groups, or roles to assign permissions to AWS resources.

**IAM controls:** `Who (authentication) can do What (authorization) in your AWS account. Authentication(who) with IAM is done with users/groups and roles whereas` **authorization(what)** `is done by policies.`

- **Roles** - are used to grant specific permission to specific actors for a set of duration of time. These actors can be authenticated by **AWS or some trusted external system**.
- User and roles use policies for authorization. Keep in mind that user and role can't do anything until you allow certain actions with a policy.
- Can be associated with AWS resources (for example EC2 instances)? No-> user, Yes->role
- AWS supports 3 Role Types for different scenarios
    - AWS service roles (for example: EC2, Lambda, Redshift,...)
    - Cross-Account Access: granting permissions to users from other AWS account, whether you control those account or not.
    - Identity Provider Access: granting permissions to users authenticated by a trusted external system. AWS supports two kinds of identity federation: - Web-based identity such as Facebook, Goolge- IAM support ingeration via OpenID Connect - SAML 2.0 identity such as Active Directory, LDAP.

#### Route 53
  is a cloud domain name system (DNS) service that has servers distributed around the globe used to translates human-readable names like www.google.com into the numeric IP addresses like 74.125.21.147.
  - scales automatically to manage spikes in DNS queries
  - allows you to register a domain name (or manage an existing)
  - routes internet traffic to the resources for your domain
  - checks the health of your resources

#### Elasticity in the Cloud
  One of the main benefits of the cloud is that it allows you to stop guessing about capacity when you need to run your applications. Sometimes you buy too much or you don't buy enough to support the running of your applications.

#### EC2 Auto Scaling
  a service that monitors your EC2 instances and automatically adjusts by adding or removing EC2 instances based on conditions you define in order to maintain application availability and provide peak performance to your users.
  - Automatically scale in and out based on needs.
  - Included automatically with Amazon EC2.
  - Automate how your Amazon EC2 instances are managed.
  - it used AWS messaging services such as 
    - Simple Notification Service (SNS) alter you, when launching and terminated 

#### Scaling group
  -  To set-up an Auto Scaling group, you require the following basic details:
    -  Count of instances - The desired count of the EC2 instances you want to have available. If any instance goes down/fails, a new instance automatically spins up.
    -  Launch template - The auto scaling group contains a collection of EC2 instances that are treated as a logical group. All EC2 instances within a group share the same configuration. Therefore, You need to specify the configuration details, such as, the ID of the Amazon Machine Image (AMI), the instance type, a key pair, security groups, and the other parameters that you use to launch EC2 instances. You specify the configuration details in a Launch template.
    -  Scaling policy - The auto scaling policy that defines how to scale your EC2 instances automatically, based on demand

#### Launch template
  As mentioned above, a launch template specifies the configuration details, such as, the ID of the Amazon Machine Image (AMI), the instance type, a key pair, security groups, and the other parameters that you use to launch EC2 instances. See a quick video below to see the information available in a launch template.

# third Module: 3. Deploy Infrastructure as Code (IAC)
#### What is (Cloud) Infrastructure?
  Cloud services are broadly categorized as 
  - Software as a Service (SaaS) 
  - Platform as a Service (PaaS)
  - Infrastructure as a Service (IaaS). &rarr; allows a user to provision Virtual Machines (VMs), set up networks (VPC), create subnets, and associate necessary security features. Further, VMs can be attached to storage volumes, different networks, or servers. All the resources mentioned above are referred to as Infrastructure.
  - Cloud is a collection of geographically distributed data centers that are logically grouped into regions and availability-zones.
  ##### the below image provide the basic services provided in Iaas
  <img src="/images/aws-services.png" />

#### Devops benefits
  - Increase the speed of software delivery
  - Increases the speed of software evolution
  - Have better reliability of the software
  - Have scalability using automation,
  - Improved collaboration among teams.
  `In other words, these tools enable a company to showcase industry best practices in software development.`
  - Issues that DevOps tries to solve:
    - Unpredictable deployments due to missmatching environment
    - Mismatched environments (development doesn’t match production)
    - Configuration Drift
#### How does it solve those problem? 
  - using automatited configuration tools 
  - dev and oper teams are communicate with each other to define the pipline and the process of running code 
  - Infrastructure as Code (IaaC) - configuration and management of cloud infrastructure using re-usable scripts.
  ##### Continuous Integration Continuous Deployment (CI/CD): 
    Tracks the development workflow from testing through production. Continuous integration is the process flow of testing any change made to your development flow, while continuous deployment tracks those changes through to staging and production systems. You may like to read this article by [Atlassian](https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment) that describes CI/CD in detail. 
  #####   Infrastructure as code (IaC): 
    Provision and manages the cloud-infrastructure by using scripts. These scripts can be written in YAML or JSON format. These scripts ensure that the same architecture can be re-built multiple numbers of times. These scripts are particularly useful in enterprise applications and different environments - dev, prod, or test. Read more [here](https://en.wikipedia.org/wiki/Infrastructure_as_code).

#### What is CloudFormation?
  is an AWS tool for creating, managing, configuring, and deploying cloud resources. This tool is beneficial if you have to provision a set of cloud resources multiple times, at scale. You can do so by simply writing (YAML or JSON) scripts that you can easily edit and run numerous times. In the script, we mention each resource's necessary configuration that we want to provision and then use the AWS CLI tool and commands to execute the scripts.
  - to configure AWS CLI you need 
    - **Access key** - It is a combination of Access Key ID and a Secret Access Key. You can generate an Access key from the AWS IAM service, and specify the level of permissions (authorization) with the help of IAM Roles.
      - it can be created from IAM tool by creating user, define the correct policy and enable `Programmatic Access`
      - Note that each user can have up to 2 access keys at the same time.
    - **Default AWS Region** - It specifies the AWS Region where you want to send your requests by default.
    - **Default output format** - It specifies how the results are formatted. It can either be a json, yaml, text, or a table.
    - **Profile** - A collection of settings is called a profile. The default profile name is `default`, however, you can create a new profile using the `aws configure --profile new_name command`.
#### What is the benefits of using it?
  - automation allow you to easliy retry the logic between  different Envs
  - Reproducible
  - you enforce your versions 

- if you have an error 
  - there is no retry 
  - there is no rollback 

#### What is intrinsic-function?
  - it is fucntion that starts with !
  - it gives you some dynamics in 
#### Configuring AWS CLI
  - `AWS configure` and follow this command by the required access key and and region us-west-2 
  - to verify that AWs already working type `aws s3 ls`
  - `aws --version`
  - `aws configure`
  - `aws configure --profile default`
  - `aws s3 ls`
  - `aws iam list-users`
  - `aws iam list-users`
  `Note that each user can have up to 2 access keys at the same time.`
  ##### quick tips 
    - ensure that you are inside the folder and see the file obviously

  - **all commands**
    - delete resource:  `aws cloudformation delete-stack --stack-name my-stack`
    - create resource: `aws cloudformation create-stack  --stack-name auto-myFirstTest --region us-east-1 --template-body file://step-1-resource.yml`
    - describe/ show the content of the stack: `aws cloudformation describe-stacks --stack-name myFirstTest`
    - if you are using the power shell file use this syntax 
      - `aws cloudformation create-stack --stack-name $1 --template-body file://$2  --parameters file://$3 --capabilities "CAPABILITY_IAM" "CAPABILITY_NAMED_IAM" --region=us-west-2`
      - Batch Script You can also try a batch script (.bat) with a similar syntax, except that the actual values can be written as `%1` instead as `$1`.

#### What is Stack in cloudFormation?
  it is agroup of resources 

#### AWS Diagrams
  are a very important starting point for planning your cloud infrastructure. DevOps engineers start with a visual representation of the required cloud infrastructure before they turn it into code. This lesson will show you how to interpret these infrastructure diagrams.
  - **Cloud Container**
    This represents your AWS account and all resources it can access.
  - **Region**
    A local business may be entirely contained in a region. So, if all your applications are always going to be deployed in the AWS Northern California region ( us-west-1 ) -for example- then you don’t need to specify this in your diagrams because all your co-workers will already know this.

    The exception would be if you are designing a multi-region architecture, say, for Disaster Recovery reasons. In this case, it’s certainly useful to specify the region you are talking about, in your diagrams.
  [reding part for diagram design](https://d1.awsstatic.com/whitepapers/building-a-scalable-and-secure-multi-vpc-aws-network-infrastructure.pdf)
  [my diagram demo](https://lucid.app/lucidchart/f07b163a-c5d8-4db3-a242-1275babf596a/edit?view_items=49GzW84IK5Ze&invitationId=inv_2fca93e4-784b-4a53-acce-202774414b83#)
  ##### Tips in Drawing the daigram 
    - Choose to have more than one availability zone to avoid a single point of failure. such as if you put everything in the same avaliability Zone AZ if something happened in this place it will go down
    - make sure that you have two of everything to ensure AZ  
    - You may choose to reduce to one AZ, possibly for prototyping and design for low cost. But it is not recommended for production environments.

##### PVC 
  - allow set of internal devices to connect to each other 
  - it is not allowed to connect the internet except you enable the internet connection 
  - it allow you to build a seperate network like if you need to add production and development Environment (completely seperate and isolated network traffic)
  - can be split further to small network using subnet 
    - you can use one subnet in one datacenter and another one in different datacenter (which allow to enforce HA if one datacenter become not avaliable you still see the second one) 
    - what is VPC and subnet 
      - the main attribute of VPC is the block of avaliable IP addresses (address base, CIDR Block)
        - example `10.0.0.0/16`
          - the first two numbers will be the fix part of this network it is similar the area code in the phone number
          - and the second two numbers represent the available IPs in the network it 256 * 256 
        - subnet it as small subset of this network 
          - it create logical seperation between resources 
          - allow easily block or allow access to resources 
          - provide service to spacific resources and not the other like public netwoek access  
    - **the main goal of PVC it to provided private IP address space for the network and resources can be used over more than one AZ,The equivalent of this would be a data center**
    - **the subnet is a subset of the VPC that allow logical seperation**
    - **/00 denote the fixed part of the network and the avaliable IPs**
    - here you can see sample diagram for the network 
  - Reference 
    - [Classes Inter Domain Routing(CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)
    - [VPCs and Subnets in AWS](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html)
  <img src="/images/sample_diagram.png" />

- **Subnets**
  - A subnet is a subset of the overall VPC network and it only exists in a single availability zone, unlike its parent network, the VPC.
  -A subnet contains resources, and can be assigned access rights that apply to all resources within that subnet.
  - Subnets can be public or private. Public subnets are accessible to external users. Private subnets are only accessed internally by other resources within your cloud container.

- **Use IP addresses for routing traffic**
  - Use IP addresses as the “keys” for routing traffic. We can route traffic to stay within the VPC, or within a particular subnet, for security reasons.
  - For example, a database or any sensitive data will be placed in a private subnet. A public server, like a web server, can be placed in a public subnet. Routing rules applied to a subnet allow us to define access to all resources placed inside that subnet.

- to use the PVC you need to attach it with the internet gateway to be accessable or you can attach VPN instead for secure connection 
- **VPN or Virtual Private Network**
  It is a type of encrypted connectivity that You can setup between your on-premise data center and your Virtual Private Cloud. This allows access in and out of your AWS VPC in a secure manner, across the internet and using internal, Private IP addresses.

- **DirectConnect**
  It is a DirectConnect is a physical data line that you can purchase directly from AWS or through a telecommunication service provider to access your AWS Cloud without moving your data traffic across the public internet.  

- **Internet Gateway**
  - An internet gateway is a resource that enables inbound and outbound traffic from the internet to your VPC.
  - An internet gateway allows external users access to communicate with parts of your VPC.
  - If you create a private VPC for an application that is internal to your company, you will not need an internet gateway.
[Read more about Internet Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html)
- **Network Address Translation (NAT) Gateway**
  It provides a way for servers/instances in the private subnet to access the Internet, for example, servers downloading the software or security patches. NAT Gateway keeps the private instances protected from unsolicited inbound connections from the Internet.
  - To provide outbound internet access to resources in the private subnets, we configure the NAT gateway to route the outbound requests to the Internet gateway for the VPC. While routing the outbound requests, the NAT gateway replaces the source instances' (private) IP address with NAT's own (public) IP address.
  - When sending the "response" traffic back to the instances, the NAT gateway translates the addresses back to the original source IP address, meaning it "translates" the incoming response into private traffic.
  - In summary, the NAT gateway serves as an intermediary to take a private resource's request, connect to the Internet, and then relay the response back to the private resource without exposing that private resource's IP address to the public.
  - **NAT Gateway needs public access itself; remember to place it in the public subnet. Place NAT gateway inside the public subnet** so that it can communicate with the public Internet and handle requests from resources in a private subnet.
**Reference:** [AWS Network Address Translation (NAT) Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)

#### Load Balancer
  - A load balancer takes incoming traffic and distributes it to two or more resources. For example, it can take inbound user requests to access your website, and it can distribute the requests evenly among two or more servers.
  - Without a load balancer, having public-facing servers in more than one AZ would mean that users would have to use a different URL to reach each of the AZs. This can be impractical compared to just a single URL.
  - resources 
    1. [Classic load balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/introduction.html)
    2. [Application load balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)
    3. [Network load balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html) 
#### Security Groups
  manage traffic at the server level (the resource level). Security Groups aren’t for managing higher-level groups such as subnets, VPC, or user accounts.
  - The same security group can be assigned to multiple resources that require the same security access settings defined by that security group.
  - You can assign an SG (firewall rule) either to an instance, load balancer, or database, etc., in your VPC. However, each SG can scope to one or more IP address anywhere on the Internet (such as giving access to yourself, allowing the entire network 0.0.0.0/0 )
  - [reading](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)
#### Routing table 
  The entries in a routing table are about information regarding how to "move traffic" not about allowing or denying traffic. A routing table defines the rules, meaning if a packet arrives for a particular destination, then it should be moved towards the defined target.


  - To allow/deny traffic at the network/subnetwork level you have ACLs!.
  - To allow/deny traffic at an instance level, you have security groups.

  - [Read more about route tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)

#### S3 (simple storage service)
  - An S3 bucket is a public service for users to upload or download files.
  - Place the S3 service outside of your VPC.
  - it contains Images, Video, large text files, log files, audit logs are all great uses for S3. Log files should be cleaned of all sensitive data prior to storing them in S3 as a shared resource for your team or company.

**Review the AWS Reference Architecture Page**
  Visit the [AWS Reference Architecture Page](https://aws.amazon.com/architecture/) and study some of the diagrams. As a case-study, pay particular attention to [WordPress Architecture](https://github.com/aws-samples/aws-refarch-wordpress), which is the CMS software that runs on nearly 60% of all sites on the internet.

##### tips 
  - you Shouldn't I include hostnames and IP addresses in my cloud diagrams

##### Example 
  <img src="/images/AWSWebApp.jpeg" />


#### cloudFormation
  [CloudForamtion Demo](/devops/2022/07/23/CloudFormationDemo.html)

