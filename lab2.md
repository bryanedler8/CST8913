```mermaid
flowchart RL
 subgraph DMZ["DMZ Network"]
        ReverseProxy["Reverse Proxy / Load Balancer<br>e.g. Nginx"]
  end
 subgraph TrustedZone["Trusted Internal Zone"]
        WebServer1["Static File Server<br>Nginx hosts React Build"]
        AppServer1["Flask App Server 1<br>Gunicorn + Flask App"]
        AppServer2["Flask App Server 2<br>Gunicorn + Flask App"]
        PrimaryDB["PostgreSQL Primary"]
        StandbyDB["PostgreSQL Standby<br>Hot Replica"]
  end
 subgraph OnPremDataCenter["On-Premises Data Center"]
        WAF["Web Application Firewall<br>WAF"]
        DMZ
        TrustedZone
  end
    WAF ----> ReverseProxy
    ReverseProxy ----> WebServer1 & AppServer1 & AppServer2
    AppServer1 ----> PrimaryDB & User["React UI"]
    AppServer2 ----> PrimaryDB
    User ----> WAF
    WebServer1 ----> User



React UI: runs in the user browser

Web application firewall: a security layer that protects the application from attacks aimed at the web applications.

Reverse Proxy / Load Balancer: Acts as the main entry point for all incoming web traffic.

Static File Server: Hosts and serves the compiled React application.

Flask Application Server: Hosts the backend application logic and API.

PostgreSQL Database: one primary and one that serves as a reblica







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


