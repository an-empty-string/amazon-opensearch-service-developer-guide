# Amazon OpenSearch Serverless \(preview\)<a name="serverless"></a>

****  
***This is prerelease documentation for Amazon OpenSearch Serverless, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only in test environments, and not in production environments\. For preview terms and conditions, see *Beta Service Participation* in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. *** 

Amazon OpenSearch Serverless is an on\-demand auto scaling configuration for Amazon OpenSearch Service\. An OpenSearch Serverless *collection* is an OpenSearch cluster that scales compute capacity based on your application's needs\. This contrasts with OpenSearch Service *provisioned OpenSearch domains*, which you manually manage capacity for\. 

OpenSearch Serverless provides a simple, cost\-effective option for infrequent, intermittent, or unpredictable workloads\. It's cost\-effective because it automatically scales compute capacity to match your application's usage\.

OpenSearch Serverless collections have the same kind of high\-capacity, distributed, and highly available storage volume that is used by provisioned OpenSearch Service domains\.

OpenSearch Serverless collections are always encrypted\. You can choose the encryption key, but you can't disable encryption\. For more information, see [Encryption at rest for Amazon OpenSearch Serverless](serverless-encryption.md)\.

**Topics**
+ [Benefits](#serverless-benefits)
+ [What is Amazon OpenSearch Serverless?](serverless-overview.md)
+ [Getting started with Amazon OpenSearch Serverless](serverless-getting-started.md)
+ [Creating and managing Amazon OpenSearch Serverless collections](serverless-manage.md)
+ [Manging capacity limits for Amazon OpenSearch Serverless](serverless-scaling.md)
+ [Ingesting data into Amazon OpenSearch Serverless collections](serverless-clients.md)
+ [Overview of security in Amazon OpenSearch Serverless](serverless-security.md)
+ [Tagging Amazon OpenSearch Serverless collections](tag-collection.md)
+ [Supported operations and plugins in Amazon OpenSearch Serverless](serverless-genref.md)
+ [Monitoring Amazon OpenSearch Serverless](serverless-monitoring.md)

## Benefits<a name="serverless-benefits"></a>

OpenSearch Serverless has the following benefits:
+ **Simpler than provisioned** – OpenSearch Serverless removes much of the complexity of managing OpenSearch clusters and capacity\. It automatically sizes and tunes your clusters, and takes care of shard and index lifecycle management\. It also manages service software updates and OpenSearch version upgrades\. All updates and upgrades are non\-disruptive\.
+ **Cost\-effective** – When you use OpenSearch Serverless, you only pay for the resources that you consume\. This removes the need for upfront provisioning and overprovisioning for peak workloads\.
+ **Highly available** – OpenSearch Serverless supports production workloads with redundancy to protect against Availability Zone outages and infrastructure failures\.
+ **Scalable** – OpenSearch Serverless automatically scales resources to maintain consistently fast data ingestion rates and query response times\.