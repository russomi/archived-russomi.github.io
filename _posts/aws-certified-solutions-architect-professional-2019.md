---
title: "AWS Certified Solutions Architect - Professional 2019"
published: false
---
## Overview
Study notes and labs...

### Data Stores

#### Concepts

##### Data Persistence

* Persistent Data Store - durable, survives reboots, restarts, power cycles
    * Glacier
    * RDS
    
* Transient Data Store - temporarily stored, passed to another process or persistent store
    * SQS
    * SNS
    
* Ephemeral Data Store - data is lost when stopped
    * EC2 instance store
    * Memcached
    
* IOPS vs. Throughput
    * Input/Output Operations per Second (IOPS) - Measure of how fast we can read and write to a device (Fast car)
    * Throughput - Measure of how much data can be moved at a time (Dump Truck)
    
* Consistency Models
    * ACID
        * Atomic
        * Consistent
        * Isolated
        * Durable
    * BASE
        * Basic Availability
        * Soft-State - not instantly consistent
        * Eventually Consistent

#### Amazon S3

* Overview
    * Released in 2006
    * Object Store
    * Max object size 5TB
    * Largest object in single PUT is 5GB
    * Use multi-part uploads if larger than 100MB
    * File Path is really a key in an Object Store

* S3 Consistency
    * Read-after-Write consistency for PUTs of new objects 
        * Create a new object that has never been asked for and you can read immediately
    * HEAD or GET before an object exists results in eventual consistency 
        * If the object has been requested before and it did not exist, it will be eventually consistent
    * Eventual Consistency for overwrite PUTs and DELETEs
        * Delete or Update object locally and replicate to other places, until then serve the existing file
    * Updates to a single key are atomic
        * Only one update at a time, processed in the order of request timestamp, and changes are see eventually

* S3 Security
    * User-based - IAM Policy
    * Resource-based - Object ACL, Bucket Policy
    * Optional MFA before Delete

* IAM -> Bucket Policy -> Object ACL
    * Is your user account allowed?
    * Does the bucket policy allow access?
    * Does the object ACL allow you to access?

* Data Protection
    * Versioning
        * New Version each write
        * Supports "Roll-Back" and "un-delete"
        * Old versions count as billable size until deleted
        * Integrated with Lifecycle Management
    * Optional MFA
        * Delete
        * Change the versioning state for a bucket
    * Cross-Region Replication
        * Security
        * Compliance
        * Latency

* Lifecycle Management
    * Optimize Storage Costs
    * Adhere to data retention policies
    * Maintain S3 volumes

* Analytics
    * Data Lake - Athena, Redshift Spectrum, QuickSight
    * IoT Streaming Data Repository - Kinesis Firehose
    * Machine Learning and AI Storage - Rekognition, Lex, MXNet
    * Storage Class Analytics - S3 Management Analytics

* Encryption
    * SSE-S3 - Use S3's existing encryption key for AES-256
    * SSE-C - Use custom AES-256 encryption key
    * SSE-KMS - Use a key generated and managed by AWS Key Management Service
    * Client-Side - Encrypt objects using your own local encryption process before uploading to S3 i.e. PGP, GPG, etc.

* Additional Features
    * Transfer Acceleration - Speed up data uploads using CloudFront in reverse
    * Requester Pays - The requester rather than bucket owner pays for requests and data transfers
    * Tags - Assign tags to objects for use in costing, billing, security, etc.
    * Events - Trigger notifications to SNS, SQS or Lambda when certain events happen in your bucket
    * Static Web Hosting - Simple and massively scalable static website hosting
    * BitTorrent - Use the BitTorrent protocol to retrieve publicly available object by automatically generating a 
    .torrent file (peer to peer)

#### Glacier

#### Elastic Block Storage

#### Elastic File System

#### Storage Gateway

#### WorkDocs

#### EC2 Databases

#### RDS

#### DynamoDB

#### Redshift

#### Neptune

#### Elasticache

#### Other Datastore Options

#### Comparing DB Options

#### Pro Tips

#### Lab - Air Quality Analysis

- [x] Identify open source data repository for air quality readings
    - [OpenAQ](https://registry.opendata.aws/openaq/) - Global, aggregated physical air quality data from public data 
    sources provided by government, research-grade and other sources. These awesome groups do the hard work of measuring 
    these data and publicly sharing them, and our community makes them more universally-accessible to both humans and 
    machines.
- [x] Setup a way to query that data via AWS Services and tools
    - [How in the world do you access air quality data older than 90 days on the OpenAQ platform? One way is to use Amazon Athena.](https://medium.com/@openaq/how-in-the-world-do-you-access-air-quality-data-older-than-90-days-on-the-openaq-platform-8562df519ecd)
    - [Accessing data older than 90 days from OpenAQ](https://gist.github.com/jflasher/573525aff9a5d8a966e5718272ceb25a)
    - [Amazon Athena User Guide](https://docs.aws.amazon.com/en_pv/athena/latest/ug/what-is.html)
    - [How to create Athena database via API](https://stackoverflow.com/questions/47625024/how-to-create-athena-database-via-api)
- [x] What city had the highest average ozone (O3) reading on October 9, 2018
    - Answer: TULARE with avg_reading = 0.056333333

##### AWS Athena CLI examples
 
```shell script
# Default Athena Bucket: arn:aws:s3:::aws-athena-query-results-565666710920-us-east-1
aws s3 mb s3://openqa-athena-output
aws athena start-query-execution --query-string "CREATE database openaqdatabase" --result-configuration "OutputLocation=s3://openqa-athena-output"
aws athena get-query-execution --query-execution-id 0202cd7a-42da-4202-b6e9-ba9bd00e77c5
aws athena start-query-execution --query-string file://openaq-table.sql --result-configuration "OutputLocation=s3://openqa-athena-output"
```

##### Create Table for openaq

```hiveql
CREATE EXTERNAL TABLE IF NOT EXISTS
  `openaqdatabase`.`openaq`(
  `date` struct<utc:string,local:string> COMMENT 'from deserializer', 
  `parameter` string COMMENT 'from deserializer', 
  `location` string COMMENT 'from deserializer', 
  `value` float COMMENT 'from deserializer', 
  `unit` string COMMENT 'from deserializer', 
  `city` string COMMENT 'from deserializer', 
  `attribution` array<struct<name:string,url:string>> COMMENT 'from deserializer', 
  `averagingperiod` struct<unit:string,value:float> COMMENT 'from deserializer', 
  `coordinates` struct<latitude:float,longitude:float> COMMENT 'from deserializer', 
  `country` string COMMENT 'from deserializer', 
  `sourcename` string COMMENT 'from deserializer', 
  `sourcetype` string COMMENT 'from deserializer', 
  `mobile` string COMMENT 'from deserializer')
ROW FORMAT SERDE 
  'org.openx.data.jsonserde.JsonSerDe' 
STORED AS INPUTFORMAT 
  'org.apache.hadoop.mapred.TextInputFormat' 
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION
  's3://openaq-fetches/realtime-gzipped'
TBLPROPERTIES (
  'transient_lastDdlTime'='1518373755')
```

##### SQL Query to find the US city with the highest average Ozone reading on 10/9/2018

```sql92
SELECT country,
         city,
         parameter,
         AVG(value) AS avg_reading
FROM openaq
WHERE parameter='o3'
        AND country='US'
        AND date.utc
    BETWEEN '2018-10-09T00:00:00.000Z'
        AND '2018-10-09T23:59:59.000Z'
GROUP BY  country, city, parameter
ORDER BY  AVG(value) DESC;
```

#### Resources
https://d1.awsstatic.com/whitepapers/Storage/AWS%20Storage%20Services%20Whitepaper-v9.pdf
https://d1.awsstatic.com/whitepapers/Multi_Tenant_SaaS_Storage_Strategies.pdf
https://d0.awsstatic.com/whitepapers/performance-at-scale-with-amazon-elasticache.pdf
https://www.youtube.com/watch?v=SUWqDOnXeDw
https://www.youtube.com/watch?v=TJxC-B9Q9tQ
https://www.youtube.com/watch?v=_YYBdsuUq2M
https://www.youtube.com/watch?v=9wgaV70FeaM


### Networking

#### Resources
https://d0.awsstatic.com/whitepapers/aws-amazon-vpc-connectivity-options.pdf
https://d1.awsstatic.com/whitepapers/Networking/integrating-aws-with-multiprotocol-label-switching.pdf
https://d1.awsstatic.com/whitepapers/Security/Networking_Security_Whitepaper.pdf
https://www.youtube.com/watch?v=KGKrVO9xlqI
https://www.youtube.com/watch?v=8gc2DgBqo9U
https://www.youtube.com/watch?v=z0FBGIT1Ub4

### Security

#### Resources
https://d1.awsstatic.com/Marketplace/scenarios/security/SEC_01_TSB_Final.pdf
https://docs.aws.amazon.com/en_pv/directoryservice/latest/admin-guide/directory_simple_ad.html
https://docs.aws.amazon.com/en_pv/IAM/latest/UserGuide/id_roles_providers.html
https://docs.aws.amazon.com/en_pv/IAM/latest/UserGuide/best-practices.html
https://aws.amazon.com/secrets-manager/faqs/
https://aws.amazon.com/blogs/security/easier-way-to-control-access-to-aws-regions-using-iam-policies/
https://docs.aws.amazon.com/en_pv/organizations/latest/userguide/orgs_manage_policies_example-scps.html#example-scp-deny-region
https://docs.aws.amazon.com/en_pv/vpc/latest/userguide/VPC_Security.html
https://docs.aws.amazon.com/en_pv/IAM/latest/UserGuide/id_credentials_temp_request.html
https://d1.awsstatic.com/whitepapers/aws-security-best-practices.pdf
https://d1.awsstatic.com/aws-answers/AWS_Multi_Account_Security_Strategy.pdf
https://d1.awsstatic.com/whitepapers/Security/DDoS_White_Paper.pdf
https://aws.amazon.com/whitepapers/?whitepapers-main.sort-by=item.additionalFields.sortDate&whitepapers-main.sort-order=desc#security
https://www.youtube.com/watch?v=gjrcoK8T3To
https://www.youtube.com/watch?v=aISWoPf_XNE
https://www.youtube.com/watch?v=tzJmE_Jlas0
https://www.youtube.com/watch?v=71fD8Oenwxc
https://www.youtube.com/watch?v=fxo67UeeN1A


### Migrations

#### Resources
https://d1.awsstatic.com/whitepapers/Migration/aws-migration-whitepaper.pdf
https://d1.awsstatic.com/whitepapers/aws_cloud_adoption_framework.pdf
https://d1.awsstatic.com/whitepapers/Migration/migrating-applications-to-aws.pdf
https://d1.awsstatic.com/whitepapers/AWS-Cloud-Transformation-Maturity-Model.pdf
https://www.youtube.com/watch?v=id-PY0GBHXA
https://www.youtube.com/watch?v=Y33TviLMBFY
https://www.youtube.com/watch?v=9wgaV70FeaM


### Architect to Scale

#### Resources
https://d1.awsstatic.com/whitepapers/aws-web-hosting-best-practices.pdf
https://d0.awsstatic.com/whitepapers/aws-scalable-gaming-patterns.pdf
https://d0.awsstatic.com/whitepapers/performance-at-scale-with-amazon-elasticache.pdf
https://d1.awsstatic.com/whitepapers/cost-optimization-automating-elasticity.pdf
https://www.youtube.com/watch?v=w95murBkYmU
https://www.youtube.com/watch?v=dPdac4LL884
https://www.youtube.com/watch?v=9TwkMMogojY
https://d0.awsstatic.com/whitepapers/architecture/AWS_Well-Architected_Framework.pdf
https://d1.awsstatic.com/whitepapers/microservices-on-aws.pdf



### Business Continuity

#### Resources
https://d1.awsstatic.com/whitepapers/Storage/Backup_and_Recovery_Approaches_Using_AWS.pdf
https://d1.awsstatic.com/whitepapers/getting-started-with-amazon-aurora.pdf
https://d1.awsstatic.com/whitepapers/architecture/AWS-Reliability-Pillar.pdf
https://www.youtube.com/watch?v=xc_PZ5OPXcc
https://www.youtube.com/watch?v=RMrfzR4zyM4
https://www.youtube.com/watch?v=a7EMou07hRc


### Deployment and Operations Management

#### Resources
https://d1.awsstatic.com/whitepapers/DevOps/infrastructure-as-code.pdf
https://d1.awsstatic.com/whitepapers/DevOps/practicing-continuous-integration-continuous-delivery-on-AWS.pdf
https://www.youtube.com/watch?v=01hy48R9Kr8
https://www.youtube.com/watch?v=Qik9LBktjgs
https://www.youtube.com/watch?v=GEPJ7Lo346A


### Cost Management

#### Resources
https://d1.awsstatic.com/whitepapers/architecture/AWS-Cost-Optimization-Pillar.pdf
https://d1.awsstatic.com/whitepapers/total-cost-of-operation-benefits-using-aws.pdf
https://d1.awsstatic.com/whitepapers/introduction-to-aws-cloud-economics-final.pdf
https://www.youtube.com/watch?v=CcspJkc7zqg
https://www.youtube.com/watch?v=XQFweGjK_-o
https://www.youtube.com/watch?v=1Z4BfRj2FiU



---

## Resources

- [AWS Certified Solutions Architect - Professional 2019](https://acloud.guru/learn/aws-certified-solutions-architect-professional-2019)
- [Registry of Open Data on AWS](https://registry.opendata.aws/)

