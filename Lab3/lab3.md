```mermaid
flowchart LR
 subgraph SecurityZone["Security Perimeter"]
        Firewall["Firewall/Router<br>Company-Operated"]
        LoadBalancer["Hardware Load Balancer"]
  end
 subgraph DMZ["DMZ Network"]
        WebServer1["Web Server 1<br>Physical Server"]
        WebServer2["Web Server 2<br>Physical Server"]
  end
 subgraph DatabaseTier["Database Tier"]
        SQLPrimary["SQL Server Primary<br>Physical Server"]
        SQLSecondary["SQL Server Secondary<br>Backup"]
  end
 subgraph StorageTier["Storage Tier"]
        FileServer1["File Server 1<br>Local File System"]
        FileServer2["File Server 2<br>Backup Storage"]
  end
 subgraph ServicesTier["Services Tier"]
        EmailServer["Email Server<br>SMTP/POP3/IMAP"]
  end
 subgraph InternalNetwork["Internal Network"]
        AppServer1["Application Server 1<br>Monolithic App"]
        AppServer2["Application Server 2<br>Monolithic App"]
        DatabaseTier
        StorageTier
        ServicesTier
  end
    Internet["Internet"] --> Firewall
    Firewall --> LoadBalancer
    LoadBalancer --> WebServer1 & WebServer2
    WebServer1 --> AppServer1
    WebServer2 --> AppServer2
    AppServer1 --> SQLPrimary & FileServer1 & EmailServer
    AppServer2 --> SQLPrimary & FileServer1 & EmailServer
    SQLPrimary --> SQLSecondary
    FileServer1 --> FileServer2
