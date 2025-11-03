
  

 ShopPro International: Cloud Migration Cost Analysis & Strategy



 1. Cost Estimate Report

This report breaks down the estimated costs into three main categories: Migration, Operational, and Management.

 1.1. Migration Cost (One-Time)

These are initial, one-time costs associated with moving the infrastructure and data to the cloud.

| Component | Description | Estimated Cost (Azure) | Rationale |
| :--- | :--- | :--- | :--- |
| Database Migration Service | Azure Database Migration Service to migrate the 5 TB SQL DB and 10 TB NoSQL DB with minimal downtime. | ~$3,200 | Based on instance hours for a large replication instance and data migration charges. |
| Data Transfer (Internet Egress) | Transferring 15 TB of warehouse data and application backups from on-premises to cloud storage. | ~$1,350 | First 10 TB is cheaper; cost increases per GB thereafter. Assumes direct internet transfer. |
| Data Transfer (Inter-Region) | Initial sync of data between regions for disaster recovery setup. | ~$1,500 | Cost to transfer 5-10 TB between US, EU, and Asia data centers. |
| Temporary "Lift & Shift" VMs | Running parallel VMs during the migration cut-over period (e.g., 2 weeks). |  ~$4,800 | Cost for running extra VMs equivalent to the production load temporarily. |
| Cloud Migration Tooling | Fees for services  Azure Migrate. | ~$0 | Azure Migrate is largely cost-free for assessment and replication. |
| Personnel & Training | (Estimate) Internal team training and potential consultant fees. | ~$15,000 | Not a cloud provider cost, but a critical project cost. |
| Total One-Time Migration Cost | | ~$25,850 | |

 1.2. Operational Cost (Monthly Recurring)

These are the ongoing costs for running the environment in the cloud. Estimates assume a mix of pay-as-you-go and reserved instances for critical components.

| Component | Configuration | Estimated Monthly Cost (Azure) |
| :--- | :--- | :--- |
| Compute (VMs) | Web Front-End (30 VMs): t3.large (Linux). API Backend (20 VMs): c5.xlarge (Linux) with Auto-Scaling. Payment (5 VMs): m5.large (Windows, PCI-Compliant). ML (5 VMs): p3.2xlarge (GPU). | ~$17,200 |
| Database | Primary DB: Managed SQL. Analytics DB: Managed NoSQL . Data Warehouse: . | ~$11,800 |
| Storage | Block Storage: 50 TB for VM disks. Object Storage: 100 TB for backups, static content. Archive Storage: 50 TB for cold backups. | ~$2,800 |
| Networking | Load Balancers: 3 Application Load Balancers (one per region). CDN: CloudFront for global content delivery (50 TB egress). NAT Gateways / VPC: Data processing fees. | ~$4,200 |
| Disaster Recovery | Geo-redundant storage replication and DR standby VMs in a secondary region. | ~$3,000 |
| Total Estimated Monthly Op Cost | | ~$39,000 |

 1.3. Management Cost (Monthly Recurring)

| Component | Description | Estimated Monthly Cost (Azure) |
| :--- | :--- | :--- |
| Monitoring & Logging | CloudWatch / Azure Monitor, Application Insights, centralized logging for 80+ VMs and services. | ~$1,500 |
| Security & Compliance | Security Hub / Microsoft Defender for Cloud, PCI DSS compliance reporting, Web Application Firewall (WAF). | ~$1,200 |
| Cost Management | Cost Explorer / Cost Management + Billing, budgeting alerts, and reporting tools. | ~$200 |
| Total Estimated Monthly Mgmt Cost | | ~$2,900 |

---

 2. Cost Optimization Strategy

To reduce the operational costs outlined above, ShopPro should implement the following strategies:

1.  Commit to Reserved Instances (RIs) / Savings Plans:
       Action: Purchase 1-year or 3-year RIs/Savings Plans for the stable, predictable workloads.
       Target: Web Front-End VMs, Primary Database, Payment Processing VMs.
       Expected Savings: 40-60% on compute costs for these resources. This could reduce the monthly operational cost by $5,000 - $7,000.

2.  Leverage Auto-Scaling and Right-Sizing:
       Action: Implement aggressive auto-scaling policies for the API Backend Microservices to scale down during off-peak hours (e.g., nights, weekdays). Perform a right-sizing analysis to ensure all VMs are using the correct CPU and Memory.
       Target: API Backend Microservices, ML training clusters (shut down when not in use).
       Expected Savings: 15-25% on compute for scalable workloads.

3.  Utilize Hybrid Benefits:
       Action: Apply Azure Hybrid Benefit for the Windows-based Payment Processing VMs by using existing on-premises Windows Server licenses.
       Target: Payment Processing VMs.
       Expected Savings: ~40% on the Windows VM license cost, saving hundreds per VM per month.

4.  Implement Storage Tiering:
       Action: Move infrequently accessed data (like old backups, logs, and historical analytics) from standard object storage to archive tiers (e.g., S3 Glacier, Azure Archive Storage).
       Target: Backup and archival data.
       Expected Savings: 70-80% on storage costs for that data.

5.  Evaluate Serverless and PaaS Options:
       Action: For net-new services or during refactoring, consider serverless options like AWS Fargate/ Lambda or Azure Container Instances/Functions for microservices. Use PaaS databases which include scaling, patching, and backups in the price.
       Target: New microservices, batch jobs.
       Benefit: Reduces management overhead and can be more cost-effective for variable workloads.

---

 3. Future Growth and Budget Plan

Assumption: 20% year-over-year growth in user traffic and data volume.

| Year | Base Monthly Op Cost (Pre-Optimization) | Key Strategic Adjustments | Projected Monthly Cost (Post-Optimization) |
| :--- | :--- | :--- | :--- |
| Year 1 | ~$40,000 | Initial migration. Heavy use of Pay-As-You-Go. Initial RI purchases for core services. | ~$40,000 |
| Year 2 | ~$48,000 (20% growth) | 1. Commit to 3-year RIs for core infrastructure. <br> 2. Refactor 30% of microservices to serverless. <br> 3. Implement aggressive storage archiving. | ~$44,000 (Only a 10% effective increase) |
| Year 3 | ~$57,600 (20% growth) | 1. Adopt Spot Instances for batch and ML workloads. <br> 2. Right-size based on Year 2 performance data. <br> 3. Leverage new, more cost-effective instance types. | ~$48,000 (Only a 9% effective increase from Year 2) |

Strategic Adjustments to Control Future Costs:

   Continuous Optimization: Make cost optimization a continuous process, not a one-time event. Use the cloud provider's cost management tools to identify idle resources and opportunities for right-sizing weekly.
   Architectural Evolution: Plan a gradual shift from IaaS (VMs) to PaaS and Serverless (Containers, Functions) for new development. This reduces operational overhead and often provides better cost-efficiency for scalable applications.
   Multi-Cloud & Negotiation: While complex, using multiple clouds for specific best-of-breed services can provide leverage for negotiating discounts with primary providers.
   Data Lifecycle Policy: Enforce strict data lifecycle policies to automatically archive or delete old data, preventing storage costs from growing uncontrollably.

References:
https://azure.microsoft.com/en-us/pricing/calculator/
https://learn.microsoft.com/en-us/azure/azure-monitor/autoscale/autoscale-overview
https://azure.microsoft.com/en-us/pricing/details/azure-sql-database/single/
https://learn.microsoft.com/en-us/azure/storage/blobs/access-tiers-overview
https://www.akamai.com/blog/cloud/cloud-migration-strategy
https://learn.microsoft.com/en-us/azure/well-architected/cost-optimization/optimize-component-costs


