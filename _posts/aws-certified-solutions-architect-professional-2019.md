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

#### S3

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

- [ ] Identify open source data repository for air quality readings
    - [OpenAQ](https://registry.opendata.aws/openaq/) - Global, aggregated physical air quality data from public data 
    sources provided by government, research-grade and other sources. These awesome groups do the hard work of measuring 
    these data and publicly sharing them, and our community makes them more universally-accessible to both humans and 
    machines.
- [ ] Setup a way to query that data via AWS Services and tools
- [ ] What city had the highest average ozone (O3) reading on October 9, 2018

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

