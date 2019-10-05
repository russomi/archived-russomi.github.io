---
title: "Advanced Cloudformation"
published: true
---
## Overview
> Manage your infrastructure as code, by mastering AWS CloudFormation. Deep-dive theory and hands-on labs 
help you achieve maximum results!

Notes and labs related to the [AWS Advanced CloudFormation Course][1].

## Section 1

* [CloudFormation Template](#CloudFormation-Template)
* [YAML in CloudFormation](#YAML-in-CloudFormation)
* [Our First Template - The Infrastructure](#Infrastructure-Template)
* Template Portability & Reuse
* Userdata
* CloudFormation Init & CFNHUP
* Creation Policies
* Deletion Policies
* Intrinsic Functions
* Parameter Constraints
* CloudFormation Service Roles
* Serverless Self-Service Portal

## Section 2

* Shared Infrastructure
* Application Environment
* AWS Custom Parameter Types
* Stack Reuse
* Improving Stack Updates
* Lambda 101
* Custom Resources Using Lambda
* Extending the template - auto subnet selection
* Theory : Automated Deployment Portal

### CloudFormation Template {#CloudFormation-Template}

Concept 1

* point 1
* point 2
* point 3

Concept 2

* point 1
* point 2
* point 3

### YAML in CloudFormation {#YAML-in-CloudFormation}

Concept 1

* point 1
* point 2
* point 3

Concept 2

* point 1
* point 2
* point 3

### Our First Template - The Infrastructure {#Infrastructure-Template}

A simple template that contains a Database, an EC2 instance, and an S3 bucket.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'A simple template intended to be run in N.Virginia (us-east-1) with a Linux (1) distro. 
  Feel free to change the ami so that you can run this in a different Region'
Resources:
  DB:
    Type: "AWS::RDS::DBInstance"
    Properties:
      AllocatedStorage: 5
      StorageType: gp2
      DBInstanceClass: db.t2.micro
      DBName: wordpress
      Engine: MySQL
      MasterUsername: wordpress
      MasterUserPassword: w0rdpr355
  EC2:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0080e4c5bc078760e # Linux (1), N.Virginia - Sept 2016
      InstanceType: t2.micro
  S3:
    Type: "AWS::S3::Bucket"
    Properties:
      BucketName: wp-xxxxxxxxxxx # replace xxxxxxxxxxx with random
```

## References
* [AWS Advanced CloudFormation Course][1]
* [AdvancedCloudFormation GitHub Repo][2]
* [GitHub Guides - Mastering Markdown][3]

---
[1]: https://acloud.guru/learn/aws-advanced-cloudformation
[2]: https://github.com/ACloudGuru/AdvancedCloudFormation
[3]: https://guides.github.com/features/mastering-markdown
