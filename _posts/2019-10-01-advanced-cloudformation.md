---
title: "Advanced Cloudformation"
published: false
---
## Overview
Notes and labs related to the [AWS Advanced CloudFormation Course][1].

---

### Data Structures in YAML

* Scalars
* Lists
* Associative Arrays

#### Scalar 

* Strings (scalars) are ordinarily unquoted, but may be enclosed in double-quotes ("), or single-quotes (').
* Block scalars are delimited with indentation with optional modifiers to preserve (|) or fold (>) newlines.

```yaml
name: "value"
age: "20"
isActive: "false"
data: | # preserves newlines
   There once was a tall man from Ealing
   Who got on a bus to Darjeeling
       It said on the door
       "Please don't sit on the floor"
   So he carefully sat on the ceiling
folded_data: >  # indentation and trailing whitespace are stripped
   Wrapped text
   will be folded
   into a single
   paragraph

   Blank lines denote
   paragraph breaks
```

#### Lists

```yaml
keyname1: ["value1", "value2"]

# or #

keyname2:
  - value1
  - value2
```

#### Associative Array

```yaml
listOfKeyValuePairs:
  - obj1key1: "obj1value"
  - obj2key1: "obj2value"

# and #

listOfObjects:
  - obj1key1: "obj1value"
    obj1key2: "obj1value"
  - obj2key1: "obj2value"
    obj2key2: "obj2value"
```

See [https://yaml.org/spec/1.2/spec.html](https://yaml.org/spec/1.2/spec.html) for details.

---

### Template Sections

See [Template Anatomy][4].

```yaml

AWSTemplateFormatVersion: "2010-09-09"

Description: |
  String

Metadata:
  template metadata

Parameters:
  set of parameters

Mappings:
  set of mappings

Conditions:
  set of conditions

Transform:
  set of transforms

Resources:
  set of resources

Outputs:
  set of outputs

```

---

### Simple template

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
[4]: https://docs.aws.amazon.com/en_pv/AWSCloudFormation/latest/UserGuide/template-anatomy.html
