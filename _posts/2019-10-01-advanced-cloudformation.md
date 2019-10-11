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

Description: >
  Here are some
  details about
  the template.

Metadata:
  Instances:
    Description: "Information about the instances"
  Databases: 
    Description: "Information about the databases"

Parameters: 
  InstanceTypeParameter: 
    Type: String
    Default: t2.micro
    AllowedValues: 
      - t2.micro
      - m1.small
      - m1.large
    Description: Enter t2.micro, m1.small, or m1.large. Default is t2.micro.
  EnvType: 
    Description: Environment type.
    Default: test
    Type: String
    AllowedValues: 
      - prod
      - test
    ConstraintDescription: must specify prod or test.

Mappings: 
  RegionMap: 
    us-east-1:
      HVM64: ami-0ff8a91507f77f867
      HVMG2: ami-0a584ac55a7631c0c
    us-west-1:
      HVM64: ami-0bdb828fd58c52235
      HVMG2: ami-066ee5fd4a9ef77f1
    eu-west-1:
      HVM64: ami-047bb4163c506cd98
      HVMG2: ami-0a7c483d527806435
    ap-northeast-1:
      HVM64: ami-06cd52961ce9f0d85
      HVMG2: ami-053cdd503598e4a9d
    ap-southeast-1:
      HVM64: ami-08569b978cc4dfa10
      HVMG2: ami-0be9df32ae9f92309

Conditions: 
  CreateProdResources: !Equals [ !Ref EnvType, prod ]

Transform:
  - CreateSubnetsPerAZ

Resources:
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType:
        Ref: InstanceTypeParameter
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", HVM64]
  MountPoint: 
    Type: "AWS::EC2::VolumeAttachment"
    Condition: CreateProdResources
    Properties: 
      InstanceId: 
        !Ref EC2Instance
      VolumeId: 
        !Ref NewVolume
      Device: /dev/sdh
  NewVolume: 
    Type: "AWS::EC2::Volume"
    Condition: CreateProdResources
    Properties: 
      Size: 100
      AvailabilityZone: 
        !GetAtt EC2Instance.AvailabilityZone
Outputs: 
  VolumeId: 
    Condition: CreateProdResources
    Value: 
      !Ref NewVolume

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

### Intrinsic Functions

See the [Intrinsic Function Reference][5].

* !FindInMap
* !Equals
* !Not
* !Or
* !And 
* !Sub
* !Ref "AWS::NoValue"
* !GetAtt
* !Join

```yaml
Parameters:
  SnapToRestore:
    Type: String
    Default: ""
    Description: snap id to restore
  EnvironmentSize:
    Type: String
    Default: SMALL
    AllowedValues:
      - SMALL
      - MEDIUM
      - LARGE
    Description: Select Environment Size (S,M,L)
  DatabaseName:
    Type: String
    Default: wordpress
  DatabaseUser:
    Type: String
    Default: wordpress
  DatabasePassword:
    Type: String
    Default: w0rdpr355
    NoEcho: true
Conditions:
  isLarge:
    !Equals [!Ref EnvironmentSize, "LARGE"]
  isntLarge:
    !Not [!Equals [!Ref EnvironmentSize, "LARGE"]]
  isRestore:
    !Not [!Equals [!Ref SnapToRestore, ""]]
Mappings:
  RegionMap:
    us-east-1:
      AMALINUX: ami-0080e4c5bc078760e
    us-east-2:
      AMALINUX: ami-0cd3dfa4e37921605
    us-west-1:
      AMALINUX: ami-0ec6517f6edbf8044
    us-west-2:
      AMALINUX: ami-01e24be29428c15b2
    sa-east-1:
      AMALINUX: ami-05145e0b28ad8e0b2
    ca-central-1:
      AMALINUX: ami-07423fb63ea0a0930
    eu-west-1:
      AMALINUX: ami-08935252a36e25f85
    eu-west-2:
      AMALINUX: ami-01419b804382064e4
    eu-west-3:
      AMALINUX: ami-0dd7e7ed60da8fb83
    eu-central-1:
      AMALINUX: ami-0cfbf4f6db41068ac
    ap-southeast-1:
      AMALINUX: ami-05b3bcf7f311194b3
    ap-southeast-2:
      AMALINUX: ami-02fd0b06f06d93dfc
    ap-northeast-1:
      AMALINUX: ami-00a5245b4816c38e6
    ap-northeast-2:
      AMALINUX: ami-00dc207f8ba6dc919
    ap-south-1:
      AMALINUX: ami-0ad42f4f66f6c1cc9
    cn-north-1:
      AMALINUX: ami-8e6aa0e3

  InstanceSize:
    SMALL:
      "EC2" : "t2.micro"
      "DB" : "db.t2.micro"
    MEDIUM:
      "EC2" : "t2.small"
      "DB" : "db.t2.small"
    LARGE:
      "EC2" : "t2.medium"
      "DB" : "db.r3.xlarge"
Resources:
  DB:
    Type: "AWS::RDS::DBInstance"
    Condition: isntLarge # added - only create the MySQL DB if its small/med
    DeletionPolicy: Snapshot
    Properties:
      AllocatedStorage: 5
      DBInstanceClass: !FindInMap [InstanceSize, !Ref EnvironmentSize, DB] # Dynamic mapping + Pseudo Parameter
      DBName: !If [isRestore, !Ref "AWS::NoValue", !Ref DatabaseName]
      Engine: MySQL
      StorageType: gp2
      MasterUsername: !If [isRestore, !Ref "AWS::NoValue", !Ref DatabaseUser]
      MasterUserPassword: !If [isRestore, !Ref "AWS::NoValue", !Ref DatabasePassword]
      DBSnapshotIdentifier: !If [isRestore, !Ref SnapToRestore, !Ref "AWS::NoValue"]
  DBAuroraCluster:
    Type: "AWS::RDS::DBCluster"
    DeletionPolicy: Snapshot
    Condition: isLarge # only create if its a large EnvironmentSize
    Properties:
      DatabaseName: !If [isRestore, !Ref "AWS::NoValue", !Ref DatabaseName]
      Engine: aurora
      MasterUsername: !If [isRestore, !Ref "AWS::NoValue", !Ref DatabaseUser]
      MasterUserPassword: !If [isRestore, !Ref "AWS::NoValue", !Ref DatabasePassword]
      SnapshotIdentifier: !If [isRestore, !Ref SnapToRestore, !Ref "AWS::NoValue"]
  DBAurora:
    Type : "AWS::RDS::DBInstance"
    Condition: isLarge # only create if its a large EnvironmentSize
    Properties:
      DBClusterIdentifier: !Ref DBAuroraCluster
      Engine: aurora
      DBInstanceClass: !FindInMap [InstanceSize, !Ref EnvironmentSize, DB]
  EC2:
    Type: "AWS::EC2::Instance"
    DeletionPolicy: Delete
    Properties:
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", AMALINUX] # Dynamic mapping + Pseudo Parameter
      InstanceType: !FindInMap [InstanceSize, !Ref EnvironmentSize, EC2]
      KeyName: AdvancedCFN
      UserData:
        "Fn::Base64":
          !Sub |
            #!/bin/bash
            yum update -y aws-cfn-bootstrap # good practice - always do this.
            /opt/aws/bin/cfn-init -v --stack ${AWS::StackName} --resource EC2 --configsets wordpress --region ${AWS::Region}
            yum -y update
            /opt/aws/bin/cfn-signal -e $? --stack ${AWS::StackName} --resource EC2 --region ${AWS::Region}
    CreationPolicy:
      ResourceSignal:
        Count: "1"
        Timeout: PT15M
    Metadata:
      AWS::CloudFormation::Init:
        configSets:
          wordpress:
            - "configure_cfn"
            - "install_wordpress"
            - "config_wordpress"
        configure_cfn:
          files:
            /etc/cfn/hooks.d/cfn-auto-reloader.conf:
              content: !Sub |
                [cfn-auto-reloader-hook]
                triggers=post.update
                path=Resources.EC2.Metadata.AWS::CloudFormation::Init
                action=/opt/aws/bin/cfn-init -v --stack ${AWS::StackName} --resource EC2 --configsets wordpress --region ${AWS::Region}
              mode: "000400"
              owner: root
              group: root
            /etc/cfn/cfn-hup.conf:
              content: !Sub |
                [main]
                stack=${AWS::StackId}
                region=${AWS::Region}
                verbose=true
                interval=5
              mode: "000400"
              owner: root
              group: root
            /var/www/html/index2.html:
              content: "test9"
          services:
            sysvinit:
              cfn-hup:
                enabled: "true"
                ensureRunning: "true"
                files:
                  - "/etc/cfn/cfn-hup.conf"
                  - "/etc/cfn/hooks.d/cfn-auto-reloader.conf"
        install_wordpress:
          packages:
            yum:
              httpd: []
              php: []
              mysql: []
              php-mysql: []
          sources:
            /var/www/html: "http://wordpress.org/latest.tar.gz"
          services:
            sysvinit:
              httpd:
                enabled: "true"
                ensureRunning: "true"
        config_wordpress:
          commands:
            01_clone_config:
              cwd: "/var/www/html/wordpress"
              test: "test ! -e /var/www/html/wordpress/wp-config.php"
              command: "cp wp-config-sample.php wp-config.php"
            02_inject_dbhost:
              cwd: "/var/www/html/wordpress"
              command: !If
                - isntLarge
                - !Sub |
                  sed -i 's/localhost/${DB.Endpoint.Address}/g' wp-config.php
                - !Sub |
                  sed -i 's/localhost/${DBAuroraCluster.Endpoint.Address}/g' wp-config.php
            03_inject_dbname:
              cwd: "/var/www/html/wordpress"
              command: !Sub |
                sed -i 's/database_name_here/${DatabaseName}/g' wp-config.php
            04_inject_dbuser:
              cwd: "/var/www/html/wordpress"
              command: !Sub |
                sed -i 's/username_here/${DatabaseUser}/g' wp-config.php
            05_inject_dbpassword:
              cwd: "/var/www/html/wordpress"
              command: !Sub |
                sed -i 's/password_here/${DatabasePassword}/g' wp-config.php
            06_store_instance_ip:
              cwd: "/var/www/html/wordpress"
              command: "curl http://169.254.169.254/latest/meta-data/public-ipv4 > ip.address"
            07a_inject_IP_1: # this allows a restore & change of IP without causing WP errors.
              cwd: "/var/www/html/wordpress"
              command: !Sub |
                myip=`cat ip.address` && echo "define('WP_SITEURL', 'http://${!myip}/wordpress');" >> wp-config.php
            07b_inject_IP_2: # this allows a restore & change of IP without causing WP errors.
              cwd: "/var/www/html/wordpress"
              command: !Sub |
                myip=`cat ip.address` && echo "define('WP_HOME', 'http://${!myip}/wordpress');" >> wp-config.php
            08a_update_theme_1:
              cwd: "/var/www/html/wordpress"
              command: !Sub |
                myip=`cat ip.address` && echo "update_option('siteurl','http://${!myip}/wordpress');" >> /var/www/html/wordpress/wp-content/themes/twentyseventeen/functions.php
            08a_update_theme_2:
              cwd: "/var/www/html/wordpress"
              command: !Sub |
                myip=`cat ip.address` && echo "update_option('home','http://${!myip}/wordpress');" >> /var/www/html/wordpress/wp-content/themes/twentyseventeen/functions.php
  S3:
    Type: "AWS::S3::Bucket"
    DeletionPolicy: Retain
Outputs:
  wproot:
    Description: Access URL for wordpress
    Value: !Join ["", ["http://", !GetAtt EC2.PublicIp, "/wordpress"]]
  wpadmin:
    Description: Admin Login URL - if restoring from snap, always go to this URL first
    Value: !Join ["", ["http://", !GetAtt EC2.PublicIp, "/wordpress/wp-login.php"]]

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
[5]: https://docs.aws.amazon.com/en_pv/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference.html
