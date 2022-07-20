---
layout: post
title: CloudFormation Template
author: Faten Elhariry
categories: Devops
tags: Devops
---
#### create cloudformation
  -  A resource must have a Type attribute, which defines the kind of AWS resource you want to create.
     -  `AWS::ProductIdentifier::ResourceType`
  - **properties** $\rightarrow$ Depending on the resource type, some properties are required
  - there are a lot of helper tools can be used with the template 
    -  **intrinsic functions**
        you can use to refer to other resources and their properties. You can use the Ref function to refer to an identifying property of a resource. Frequently
        - Ref Function 
          - The intrinsic function Ref returns the value of the specified parameter or resource.
          - When you specify a parameter's logical name, it returns the value of the parameter.
          - When you specify a resource's logical name, it returns a value that you can typically use to refer to that resource, such as a physical ID.
      ```
      Resources:
          Ec2Instance:
            Type: 'AWS::EC2::Instance'
            Properties:
              SecurityGroups:
                - !Ref InstanceSecurityGroup
              KeyName: mykey
              ImageId: ''
          InstanceSecurityGroup:
            Type: 'AWS::EC2::SecurityGroup'
            Properties:
              GroupDescription: Enable SSH access via port 22
              SecurityGroupIngress:
                - IpProtocol: tcp
                  FromPort: 22
                  ToPort: 22
                  CidrIp: 0.0.0.0/0
      ```
    The Ref function is handy if the parameter or the value returned for a resource is exactly what you want; however, you may need other attributes of a resource. For example, if you want to create a CloudFront distribution with an S3 origin, you need to specify the bucket location by using a DNS-style address. A number of resources have additional attributes whose values you can use in your template. To get these attributes, you use the Fn::GetAtt function.
    ```
    Resources:
      myBucket:
        Type: 'AWS::S3::Bucket'
      myDistribution:
        Type: 'AWS::CloudFront::Distribution'
        Properties:
          DistributionConfig:
            Origins:
              - DomainName: !GetAtt 
                  - myBucket
                  - DomainName
                Id: myS3Origin
                S3OriginConfig: {}
            Enabled: 'true'
            DefaultCacheBehavior:
              TargetOriginId: myS3Origin
              ForwardedValues:
                QueryString: 'false'
              ViewerProtocolPolicy: allow-all
    ```
    - **Specifying conditional values using mappings**
