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

#### Amazon Glacier

* Overview
    * Cheap, slow to respond, seldom accessed
    * "Cold Storage"
    * Used by AWS Storage Gateway Virtual Table Library
    * Integrated with AWS S3 via Lifecycle Management
    * Faster retrieval speed options if you pay more

* Glacier - Standalone service
    * Glacier Vault - Analogous to S3 Bucket
    * Archive - Analogous to S3 Object, immutable, file, zip, Max Size 40TB 
    * Glacier Vault Lock Policy - immutable, different than access policy, applies rules (i.e. no delete allowed)
    * Access Policy - IAM policy to allow users to access to the Glacier Vault or Glacier Vault Lock

* Vault Lock vs. Access Policy

> a vault lock policy can be locked to prevent future changes, providing strong enforcement for your compliance 
> controls. You can use the vault lock policy to deploy regulatory and compliance controls, which typically require 
> tight controls on data access. In contrast, you use a vault access policy to implement access controls that are not 
> compliance related, temporary, and subject to frequent modification. Vault lock and vault access policies can be used 
> together. For example, you can implement time-based data retention rules in the vault lock policy (deny deletes), 
> and grant read access to designated third parties or your business partners (allow reads).

#### Elastic Block Storage

* Overview
    * Virtual Hard drives
    * Only used with EC2
    * Tied to a single AZ
    * Choices for IOPS, Throughput, and Cost
    * Snapshots
    * You buy 64 blocks but are only using 4
    * Detach and Attach to a different instance
    
* Instance Stores
    * Temporary
    * Locked to a specific EC2 instance
    * Ideal for caches, buffers, work areas
    * Data goes away when EC2 is stepped or terminated
    * Slightly faster than EBS (direct attached storage vs. NAS)

* Snapshots
    * Cost-effective and low effort backup strategy
    * Share dataset with other user or accounts
    * Used to migrate to a new AZ or region
    * Convert unencrypted volume to an encrypted volume
    * Snapshots record changes and does not equate to total size of volume
    * Collection of pointers stored in S3
    * Data Lifecycle Manager for EBS Snapshots
    * Automate the creation and deletion of EBS snapshots based on a schedule, targeted by tags
    * Retention rules to delete stale snapshots and save on costs

#### Elastic File System

* Overview
    * NFS released by Sun 1984
    * Implementation of NFS file share protocol, not secure
    * Elastic storage capacity and pay for only what you use (contrast to EBS)
    * Multi-AZ metadata and data storage
    * Configure mount-points in one or many AZs
    * In theory, can be mounted from on-premises systems... 
        * needs HA connectivity, not secure
        * Alternative to use is AWS DataSync
            * Purpose built protocol, secure
            * Keeps storage on-prem in sync with EFS or S3
            * Also supports EFS-to-EFS sync
    * Cost is a factor, 3x cost to EBS, 20x cost to S3
    * Some NFS4 features are not used
    
#### Storage Gateway

* Overview
    * Virtual machine that runs on-prem with VMWare or HyperV or Dell hardware appliance
    * Provides local storage resources backed by AWS S3 and Glacier
    * Often used in disaster recovery preparedness to sync to AWS
    * Useful in cloud migrations
    
* Operation Modes
    * File Gateway - NFS, SMB - Allow on-prem or EC2 instances to store objects in S3 via FNS or SMB mount point
    * Volume Gateway Stored Mode (Gateway-stored Volumes) - ISCSI - Async replication of on-prem data to S3
    * Volume Gateway Cached Mode (Gateway-cached volumes) - ISCSI - Primary data stored on S3 with frequently access 
    data cached locally on-prem
    * Tape Gateway (Gateway-Virtual Tape Library) - ISCSI - Virtual media changer and tape library for use with 
    existing backup software

#### WorkDocs

* Overview
    * Secure, fully managed file collaboration service
    * Can integrate with AD for SSO
    * Web, mobile and native clients (no linux client)
    * Supports HIPPA, PCI DSS and ISO compliance requirements
    * Available SDK for creating complementary apps

#### EC2 Databases

* Overview
    * Run any database with FULL control and ultimate flexibility
    * Must manage everything like backups, redundancy, patching, scale
    * Option if you require a database not yet supported by RDS such as IBM DB2 or SAP HANA
    * Option if it is not feasible to migrate to AWS-managed database
    * See the Quickstart for [SAP HANA on AWS](https://aws.amazon.com/quickstart/architecture/sap-hana/)

#### RDS

* Overview
    * Managed database option for 
        * MySQL
        * Maria
        * PostgresSQL 
        * Microsoft SQL Server 
        * Aurora - Oracle and MySQL-compatible 
    * Best for structured, relational data store needs
    * Aims to be a drop-in replacement for existing on-prem instances (same database)
    * Automated backups and patching
    * User defined maintenance windows
    * Automated scaling, replication, and redundancy
    
* Anti-Patterns
    * Large binary objects (BLOBs) - use S3
    * Automated scalability - use DynamoDB
    * Name/Value Data Structures - use DynamoDB
    * Unstructured or unpredictable - use DynamoDB
    * Other database platforms like IBM DB2 or SAP HANA - use EC2
    * You need complete control over the database - use EC2
    
* Redundancy
    * Multi-AZ RDS (Master/Stand-by/Read Replica) - Sync replication in region
        * Automated promotion of Stand-by to Master on AZ failure
    * Cross Region Read Replicas - Async replication across regions
        * Manual two-step process to promote Read Replica to new Master on Region Failure
        * This could be automated, but not recommended
    * Non-transactional storage engines do not support replication
        * Must use InnoDB or XtraDB on Maria (fork of MySQL)


#### DynamoDB

* Overview
    * Managed, multi-AZ NoSQL data store with Cross-Region Replication option
    * Default is eventually consistent with reads, but can request strongly consistent read via SDK
    * Priced on throughput, rather than compute
    * Provision read and write capacity in anticipation of need
    * Autoscale capacity adjusts per configured min/max levels
    * On-Demand Capacity for flexible capacity at a small premium cost
    * Achieve ACID compliance with DynamoDB Transactions
    
* Relational vs NoSQL
    * Tables and Fields vs. Name Value Pairs
    * Primary key must be unique aka Partition key
    * To read, only need primary key
    * A composite primary key - Partition key and Sort key
        * Order Number and Timestamp

* Secondary Indexes
    * Global Secondary Index - Partition Key and Sort Key can be different from those on the table
        * Not restricted, but Global
    * Local Secondary Index - Same partition key as the table, but different sort key
        * Stay local, respect the partition key but customize the sort key 
    * Max 5 local and 5 global
    * Max 20 attributes across all indexes
    * Indexes take up storage space

* When to use Global vs. Local
    * Global Secondary Index
        * When you want a fast query of attributes outside the primary key -- without having to do a table scan
        * "I'd like to query Sales Orders by Customer number rather than Sales Order Number..."
    * Local Secondary Index
        * When you already know the partition key and want to quickly query on some other attribute
        * "I have the Sales Order Number, but I'd like to retrieve only those records with a certain Material Number..."
    * Attribute Projections
        * Secondary Index is similar to a view
        * You can choose which attributes are projected into the index

* Secondary Indexes - if you need to...
    * ... access just a few attributes the fastest way possible
        * Consider: Consider projecting attributes in a Global Secondary index
        * Cost: Minimal
        * Benefit: Lowest possible latency access for non-key items
    * ... frequently access some non-key attributes
        * Consider: Projecting those attributes in a Global Secondary index
        * Cost: Moderate, aim to offset cost of table scans
        * Benefit: Lowest possible latency access for non-key items
    * ... frequently access most non-key attributes
        * Consider: Projecting those attributes or even the entire table in a global secondary index
        * Cost: Up to Double
        * Benefit: Maximum Flexibility
    * ... rarely query but write or update frequently
        * Consider: Projecting keys only for the global secondary index
        * Cost: Minimal
        * Benefit: Very fast write or updates for non-partition key items

* DynamoDB Design Best Practices
    * Partition Key with different values for the sort key
    * Sparse indexes ... not every record has the indexed attribute in a Secondary Index
    * Aggregations can be supported by creating a global secondary index on date/period value
    * Create table replicas with Secondary Indexes
        * Could be used to support High Write on Table and High Read on Secondary Index
        * Support customer tiers where basic tier has lower read/write capacity against index, 
        premium tier has higher read/write against table
    
#### Redshift

* Overview
    * Fully managed, clustered peta-byte scale data warehouse
    * Extremely cost-effective as compared to some other on-premises daa warehouse platforms
    * PostgreSQL compatible with JDBC and ODBC drivers available
    * Compatible with most BI tools out of the box
    * Features parallel processing and columnar data stores which are optimized for complex queries
    * Redshift Spectrum - Option to query directly from data files on S3

* Data Lake
    * Query raw data without extensive pre-processing
    * Lessen time from data collection to data value
    * Identify correlations between disparate data sets

#### Neptune

* Overview
    * Fully-managed graph database
    * Supports open graph APIs for both Gremlin and SPARQL
    * Relationships between objects
    * Social Media

#### Elasticache
    
* Overview
    * cache = pronounced like cash
    * Fully managed implementations of two popular in-memory data stores - Redis and Memcached
    * Automated scalability for memory, writes, reads
    * Transient in-memory key/value store - not persistent 
        * Redis does support backups and restores
    * Billed by node size and hours of usage
    
* Use / Benefit
    * Web Session Store - In cases with load-balanced web servers, store web session information in Redis to decouple 
    web frontend from web session and make the web tier stateless
    * Database Caching - Use Memcached in front of AWS RDS to cache popular queries to offload work from RDS and return
    results faster
    * Leaderboards - Use Redis to provide a live leaderboard for millions of users of your mobile app
    * Streaming Data Dashboards - Provide a landing spot for streaming sensor data on the factory floor, live real-time
    dashboard displays

* Memcached vs. Redis
    * Memcached
        * Simple, no-frills, straight-forward
        * Supports scale out/in as demand changes
        * You need to run multiple CPU cores and threads
        * You need to cache objects (i.e. database queries)
    * Redis
        * You need encryption
        * You need HIPAA compliance
        * Support for clustering
        * Complex data types
        * High Availablility (replication)
        * Pub/Sub capability
        * Geospacial Indexing
        * Backup and Restore

#### Other Datastore Options

* Amazon Athena
    * SQL Engine overlaid on S3 based on Presto
    * Query raw data objects as they sit in an S3 bucket in .csv or .json format
    * Use or convert your data to Parquet format for performance
    * Similar to Redshift Spectrum but...
        * Use Amazon Athena when data lives on S3 without the need to perform joins with other data sources
        * Use Redshift Spectrum when you want to join S3 data with existing Redshift tables or create union products
        
* Amazon Quantum Ledger Database
    * Based on blockchain concepts
    * Provides an immutable and transparent journal as a service
    without having to setup and maintain an entire blockchain framework
    * Centralized design (as opposed to decentralized consensus-based design for common blockchain frameworks)
    supports higher performance and scalability
    * Append-only concept where each record contributes to the integrity of the chain

* Amazon Managed Blockchain
    * Fully managed blockchain framework supporting open source frameworks of Hyperledger Fabric and Ethereum
    * Distributed consensus-based concept consists of a network, members (or other AWS Accounts), nodes (instances), 
    and potentially applications
    * Uses the Amazon QLDB ordering service to maintain complete history of transactions

* Amazon Timestream Database
    * Fully managed database service specifically built for storing and analyzing time-series data
    * Alternative to DynamoDB or Redshift
    * Built in analytics like interpolation and smoothing
    * Use Cases
        * Industrial Machinery
        * Sensor Networks
        * Equipment Telemetry

* Amazon DocumentDB
    * MongoDB compatibility
    * Emulates the MongoDB API so it acts like MongoDB to existing clients and drivers
    * Fully managed with multi-AZ, scalable, integrated with KMS, backed up to S3
    * Option if you currently use MongoDB and want to get out of the server management business
    
* Amazon ElasticSearch (ES)
    * Mostly a search engine but also a document store
    * Amazon ElasticSearch Service aka ELK stack
    * Intake data from CloudWatch, Firehose, IoT

#### Database Comparisons

* Options
    * Database on EC2
        * Ultimate control over database
        * Preferred DB not supported on RDS
    * Amazon RDS
        * Need traditional relation database for OLTP
        * Your data is well-formed and structured
    * Amazon DynamoDB
        * Name/Value pair data or unpredictable data structure
        * In-memory performance with persistence
    * Amazon Redshift
        * Massive amounts of data
        * Primary OLAP workloads
    * Amazon Neptune
        * Relationships between objects a major portion of data value
    * Amazon Elasticache
        * Fast temporary storage for small amounts of data
        * Highly volatile data
        
#### Pro Tips

* Archiving and Backup is often a great "pilot" project to build AWS business case
* Make use of the S3 endpoints within your VPC
* Learn how to properly secure your S3 bucket
* Encrypt Everything!
* Consider Aurora for your production MySQL/Maria or PostgreSQL needs
* Consider NoSQL if you don't need relational database features
* Databases on EC2 cost less, but requires management (backups, patching, OS-level hardening)
* There can be a performance hit when RDS backups run if you have only a single AZ instance

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

https://docs.aws.amazon.com/en_pv/elasticloadbalancing/latest/network/introduction.html
https://docs.aws.amazon.com/en_pv/vpc/latest/userguide/vpc-nat-comparison.html
https://docs.aws.amazon.com/en_pv/directconnect/latest/UserGuide/getting_started.html
https://docs.aws.amazon.com/en_pv/vpc/latest/userguide/vpc-network-acls.html#nacl-ephemeral-ports
https://docs.aws.amazon.com/en_pv/vpc/latest/userguide/vpc-ip-addressing.html
https://docs.aws.amazon.com/en_pv/vpc/latest/userguide/VPC_DHCP_Options.html
https://docs.aws.amazon.com/en_pv/vpc/latest/userguide/VPC_Subnets.html
https://aws.amazon.com/dms/
https://docs.aws.amazon.com/en_pv/emr/latest/ManagementGuide/emr-plan-file-systems.html
https://docs.aws.amazon.com/en_pv/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-s3-messages.html
https://docs.aws.amazon.com/en_pv/streams/latest/dev/developing-consumers-with-kcl.html
https://docs.aws.amazon.com/en_pv/amazondynamodb/latest/developerguide/HowItWorks.Partitions.html
https://d1.awsstatic.com/whitepapers/aws-scalable-gaming-patterns.pdf
https://docs.aws.amazon.com/en_pv/autoscaling/ec2/userguide/scaling_plan.html
https://docs.aws.amazon.com/en_pv/amazondynamodb/latest/developerguide/bp-time-series.html
https://aws.amazon.com/rds/aurora/faqs/
https://d1.awsstatic.com/whitepapers/architecture/AWS-Reliability-Pillar.pdf
https://docs.aws.amazon.com/en_pv/AWSEC2/latest/UserGuide/placement-groups.html#placement-groups-spread
https://aws.amazon.com/redshift/faqs/
https://media.amazonwebservices.com/AWS_Disaster_Recovery.pdf
https://d1.awsstatic.com/whitepapers/overview-of-deployment-options-on-aws.pdf
https://docs.aws.amazon.com/en_pv/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html
https://d1.awsstatic.com/whitepapers/overview-of-deployment-options-on-aws.pdf
https://aws.amazon.com/ec2/dedicated-hosts/
https://d1.awsstatic.com/whitepapers/architecture/AWS-Cost-Optimization-Pillar.pdf
