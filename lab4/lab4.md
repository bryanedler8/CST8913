1.	Solution Diagram:
```
flowchart LR
 subgraph NodePool1["Node Pool - Primary Region"]
        Pod1["Web App Pod<br>Node.js Container"]
        Pod2["Web App Pod<br>Node.js Container"]
        Pod3["Web App Pod<br>Node.js Container"]
  end
 subgraph AKSCluster["Azure Kubernetes Service AKS"]
        NodePool1
        Ingress["Ingress Controller<br>Load Balancer"]
  end
 subgraph AzureCloud["Azure Cloud Environment"]
        AKSCluster
        SQLPrimary["Azure SQL Database<br>Primary - Region 1"]
        SQLSecondary["Azure SQL Database<br>Secondary - Region 2"]
        VNet["Virtual Network VNet"]
        FW["Azure Firewall"]
        LB["Azure Load Balancer"]
  end
    Internet["Internet Users"] --> FW
    FW --> LB
    LB --> Ingress
    Ingress --> Pod1 & Pod2 & Pod3
    Pod1 --> SQLPrimary
    Pod2 --> SQLPrimary
    Pod3 --> SQLPrimary
    SQLPrimary --> SQLSecondary

    style AKSCluster fill:#e1f5fe
    style SQLPrimary fill:#f3e5f5
    style SQLSecondary fill:#f3e5f5


```

2.	A description of the target architecture:
The target architecture will include an Azure Firewall the links the internet with an Azure load balancer. To make use of Azure Kubernetes Service (AKS), The load balancer is connected to an Ingress Controller load balancer, that manages external HTTP-like traffic access to services within a cluster.  The Ingress load balancer is connected to multiple Web app pod Node.js containers, which will be connected to the primary Azure SQL Database. The primary Database will be connected to the secondary database as a backup.

3.	The steps of migration:
a.	Containerization of the web application:
1.	Creating Dockerfile and .dockerignore
2.	Building and testing container image locally
3.	Pushing the image to Azure Container Registry 
4.	Creating  Kubernetes deployment manifest
b.	Migration of the database to a managed SQL service.
1. Pre-migration
   - Perform full database backup
   - Run Data Migration Assistant for compatibility check
   - Resize Azure SQL DB based on current usage

2. Migration Execution
   - Start continuous data replication
   - Monitor replication lag and performance
   - Plan 6-hour downtime window for cutover
c.	Configuration of the Kubernetes cluster for high availability.
Best practice for Configuration of the Kubernetes:
-	Specifying the latest stable API version.
-	Storing Configuration files in version control before pushing them into the cluster. This allows you to quickly roll back a configuration change if necessary. It also aids cluster re-creation and restoration.

-	Writing configuration files using YAML rather than JSON. Though these formats can be used interchangeably in almost all scenarios, YAML tends to be more user-friendly.

-	Grouping related objects into a single file whenever it makes sense. One file is often easier to manage than several.

 
