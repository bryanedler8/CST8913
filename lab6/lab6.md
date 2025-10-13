# Cloud Resource Descriptions and Comparison Table:

| #  | Description | AWS (Service Name) | Azure (Service Name) | Google Cloud (Service Name) |
|----|-------------|--------------------|----------------------|----------------------------|
| 1  | A compute service that provides scalable virtual machines for running applications. | Amazon EC2 | Azure Virtual Machines | Google Compute Engine |
| 2  | An object storage service used to store and retrieve data, commonly used for backups and static website content. | Amazon S3 (Simple Storage Service) | Azure Blob Storage | Google Cloud Storage |
| 3  | A managed relational database service that supports multiple database engines like MySQL, PostgreSQL, and SQL Server. |Amazon RDS (Relational Database Service)  | Azure SQL Database / Azure Database for MySQL/PostgreSQL | Cloud SQL |
| 4  | A serverless compute service that allows you to run code in response to events without provisioning or managing servers. | AWS Lambda | Azure Functions | Google Cloud Functions |
| 5  | A virtual private network service that allows you to create isolated networks within the cloud provider's infrastructure. | Amazon VPC (Virtual Private Cloud) | Azure Virtual Network (VNet) | Google Cloud VPC (Virtual Private Cloud) |
| 6  | A content delivery network (CDN) service that delivers data, videos, applications, and APIs to customers around the world with low latency. | Amazon CloudFront | Azure CDN | Google Cloud CDN |
| 7  | A managed NoSQL database service designed for low-latency, high-scale applications. | Amazon DynamoDB | Azure Cosmos DB | Google Cloud Firestore / Datastore |
| 8  | A block storage service for use with virtual machines, offering persistent storage for data. | Amazon EBS (Elastic Block Store) | Azure Managed Disks | Google Persistent Disk |
| 9  | A managed container orchestration service based on Kubernetes. | Amazon EKS (Elastic Kubernetes Service) | Azure Kubernetes Service (AKS) |Google Kubernetes Engine (GKE)|
| 10 | A service for managing user access and encryption keys to secure cloud resources. |AWS IAM + AWS KMS | Azure AD + Azure Key Vault | Google Cloud IAM + Cloud KMS |
| 11 | A platform that automates application deployment and scaling without needing to manage infrastructure. | AWS App Runner / Elastic Beanstalk | Azure App Service / Container Apps | Google Cloud Run / App Engine |
| 12 | A service that provides monitoring and logging of applications and infrastructure, offering insights into resource usage and performance. | Amazon CloudWatch | Azure Monitor | Google Cloud Monitoring + Cloud Logging |
| 13 | A domain name system (DNS) service that routes traffic globally and translates domain names to IP addresses. | Amazon Route 53 | Azure DNS | Google Cloud DNS |
| 14 | A load balancing service that distributes incoming network traffic across multiple targets, improving application availability. | Elastic Load Balancing (ELB) | Azure Load Balancer / Application Gateway | Google Cloud Load Balancing |
| 15 | A service that automatically scales your cloud infrastructure based on demand, ensuring resources are available as needed. | AWS Auto Scaling | Azure Autoscale / Virtual Machine Scale Sets | Google Cloud Autoscaler |
| 16 | A message queuing service that enables applications to send and receive messages between different components. | Amazon SQS  | Azure Queue Storage / Service Bus | Google Cloud Pub/Sub |
| 17 | A managed real-time data streaming service that collects and processes large amounts of data from various sources. | Amazon Kinesis | Azure Event Hubs / Stream Analytics | Google Cloud Dataflow / Pub/Sub |
| 18 | A fully managed, highly scalable data warehouse service optimized for analytics and large-scale queries. | Amazon Redshift | Azure Synapse Analytics	 | Google BigQuery |
| 19 | A service that automates the execution of workflows and allows the integration of different cloud services in a sequence of steps. | AWS Step Functions | Azure Logic Apps | Google Cloud Workflows |
| 20 | A service that integrates multiple data sources and enables data migration, transformation, and movement across platforms. | AWS Glue/ AWS Database Migration Service (DMS) | Azure Data Factory | Google Cloud Data Fusion / Dataflow |
| 21 | A data catalog and governance service that helps manage metadata across your data estate, supporting compliance and security. | AWS Glue Data Catalog / AWS Lake Formation | Azure Purview | Google Cloud Data Catalog / Dataplex |
| 22 | A set of machine learning and AI services that provide pre-built models, APIs, and tools for developers to easily implement AI in their apps. | Amazon AI Services / SageMaker | Azure AI Services / Cognitive Services | Google Cloud AI |
| 23 | A service that allows you to define and deploy infrastructure using code, automating the management of cloud resources. | AWS CloudFormation | Azure Resource Manager (ARM) Templates | Google Cloud Deployment Manager |
| 24 | A fully managed CI/CD service that automates the building, testing, and deployment of applications to production environments. | AWS CodePipeline / CodeBuild / CodeDeploy | Azure DevOps Pipelines / Azure Pipelines | Google Cloud Build / Cloud Deploy |
| 25 | A desktop as a service (DaaS) offering that allows you to deploy virtual desktops in the cloud and access them remotely. | Amazon WorkSpaces | Azure Virtual Desktop (AVD) | Google Cloud VMware Engine (with Horizon) |
| 26 | A backup and disaster recovery service that helps to protect your data by creating backups and replicas of your cloud resources. | AWS Backup | Azure Backup / Site Recovery | Google Cloud Backup and DR |
| 27 | A service designed for big data analytics, allowing organizations to store, process, and analyze large datasets in real time. | Amazon EMR (Elastic MapReduce) | Azure HDInsight / Databricks | Google Cloud Dataproc |
| 28 | A file storage service for storing and sharing files with users, typically used in shared file systems across applications. | Amazon EFS (Elastic File System) | Azure Files | Google Cloud Filestore |
| 29 | A service that helps you transcode, process, and stream media content such as video and audio. | AWS Elemental MediaConvert / MediaLive | Azure Media Services | Google Cloud Video Transcoder API |
| 30 | A real-time communication service used for sending notifications, emails, and text messages to users and devices. | Amazon SNS (Simple Notification Service) | Azure Notification Hubs / Communication Services | Google Cloud Pub/Sub / Firebase Cloud Messaging |
```
Cloud Services Comparison: AWS vs Azure vs Google Cloud

The Basics

When we look at the three major cloud providers - Amazon Web Services (AWS), Microsoft Azure, and Google Cloud,
they all offer pretty much the same types of services, but with different names and some unique strengths.

How They're Similar

They All Have the Same Core Services:

•	Virtual Servers: AWS calls them EC2, Azure says Virtual Machines, Google says Compute Engine
•	Cloud Storage: AWS has S3, Azure has Blob Storage, Google has Cloud Storage
•	Serverless Computing: AWS Lambda vs Azure Functions vs Google Cloud Functions
•	Managed Databases: All offer SQL and NoSQL options

Common Benefits Across All Three
•	Pay for what you use (no upfront costs)
•	Automatic scaling when traffic increases
•	Built-in security features
•	Global data centers

What Makes Each Provider Unique

AWS 
•	Has the most services 
•	Been around the longest 
•	Huge community and documentation
•	Best for companies wanting the most options
Cool Features:
•	AWS Lambda started the serverless trend
•	Amazon S3 is the industry standard for cloud storage
•	Extensive global network

Azure

•	Perfect for companies using Microsoft products
•	Great for hybrid cloud (mixing cloud and on-premises)
•	Strong security and compliance features
•	Excellent for Windows-based applications
Cool Features:
•	Seamless integration with Office 365 and Windows
•	Azure Arc manages multiple cloud environments
•	Strong AI and machine learning tools

Google Cloud

•	Best for data analytics and AI/ML
•	Excellent container and Kubernetes support
•	Strong in open source technologies
•	Great for startups and tech companies
Cool Features:
•	BigQuery for super-fast data analysis
•	Originally created Kubernetes
•	Advanced machine learning capabilities

Naming Differences

AWS Names
•	Often include "Amazon" or "AWS"
•	Technical sounding names
•	Lots of acronyms (EC2, S3, RDS)
•	Example: Amazon Elastic Compute Cloud (EC2)

Azure Names
•	Always start with "Azure"
•	Business-friendly names
•	Easier to understand
•	Example: Azure Virtual Machines

Google Cloud Names
•	Usually start with "Google Cloud" or "Cloud"
•	Clean, modern names
•	Focus on functionality
•	Example: Google Compute Engine


