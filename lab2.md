```mermaid
flowchart LR
 subgraph ServerlessBackend["Serverless Backend"]
        APIGW["API Gateway"]
        Lambda["AWS Lambda<br>Flask Application"]
  end
 subgraph ServerlessDB["Serverless Database"]
        Aurora["Aurora Serverless<br>PostgreSQL-Compatible"]
  end
 subgraph CloudProvider["Cloud Provider AWS"]
        CF["CloudFront CDN"]
        ServerlessBackend
        ServerlessDB
        S3["S3 Bucket<br>React Build Files"]
  end
    User["User/Client"] -- HTTPS Requests --> CF
    CF -- Serves Static Files from Edge --> User
    CF -- API Requests /api/* --> APIGW
    APIGW -- Invokes --> Lambda
    Lambda -- SQL Queries --> Aurora
    S3 -- Origin for Static Files --> CF


