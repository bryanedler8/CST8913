

---

### **Tailwind Traders: 3-Tier Application Migration Plan**

### **Task 1 – Set Up Tooling for Discovery**

**1. Do you use agent-based, agentless, or a mix for discovery?**
*   **A mix, but primarily agentless.** I will use the **Azure Migrate: Discovery and assessment** tool with the Azure Migrate appliance for agentless discovery of the VMware VMs (WEB01, WEB02, APP01). This is better because it requires no installation on the VMs.
*   As for the physical SQL01 server, I will use the **Azure Migrate appliance in a physical server configuration** which is also agentless.

**2. How many appliances do you need and why?**
the applications that will be needed are:
*   **One Azure Migrate appliance** configured for the VMware vCenter/ESXi host to discover WEB01, WEB02, and APP01.
*   **A second, separate Azure Migrate appliance** configured for physical/server discovery to discover SQL01.
*   *The Reason:* A single appliance can only be associated with one vCenter or physical environment. Since SQL01 is a physical server outside of the VMware environment, it requires its own dedicated appliance.

**3. What credentials must be prepared for:**
*   **Software Inventory:** VMware vCenter/ESXi credentials with read-only access. For the physical server, local Windows Administrator credentials for SQL01.
*   **SQL Discovery:** Windows authentication credentials on the servers hosting SQL Server, with `sysadmin` role permissions on the SQL Server instances.
*   **Dependency Mapping:** For agentless dependency analysis, the same Windows Administrator credentials used for discovery are required. If using agent-based (MMA/AMA), agents would need to be installed.

**4. What are 3 best practices Tailwind should follow while running discovery?**
1.  **Ensure Sufficient Permissions:** Validate that the provided vCenter and Windows credentials have the necessary read-only permissions to perform a comprehensive discovery and dependency analysis.
2.  **Size the Appliance Correctly:** Deploy the Azure Migrate appliance on a VM that meets or exceeds the recommended sizing to handle the performance data collection.
3.  **Run Discovery for a Full Business Cycle:** Allow the discovery and performance data collection to run for a sufficient time to capture a complete picture of resource utilization, including any weekly or monthly peaks.

---

### **Task 2 – Perform Assessment Planning**

**1. What assessment type should Tailwind choose (production vs non-production)?**
*   **Production Assessment.** This will reflect the actual performance requirements of the live application and ensures the Azure VMs are right-sized to handle the real workload. It will also help avoiding both over-provisioning and under-provisioning.

**2. What target region and performance-history duration should be used?**
*   **Target Region:** East US 2 (or the region closest to the company's primary user base that meets compliance needs).
*   **Performance-History Duration:** **30 days**. This captures a full business cycle and provides a more accurate picture than a 7 or 1-day duration.

**3. Should the company use performance-based sizing or on-premises VM size? Explain why.**
*   **Performance-based sizing.** This is the recommended best practice. It analyzes the actual CPU, memory, and disk utilization of the on-premises servers and recommends appropriately sized Azure VMs. Using the "on-premises" size would simply map the existing, often over-provisioned, hardware specs to similar Azure VMs, leading to unnecessary cost.

**4. What comfort factor, pricing model, and licensing model will you select?**
*   **Comfort Factor:** 95th percentile. This is standard for production workloads and helps filter out brief performance spikes.
*   **Pricing Model:** Pay-As-You-Go. This offers maximum flexibility.
*   **Licensing Model:** **Azure Hybrid Benefit** will be selected, as Tailwind Traders is likely using licensed Windows Server and SQL Server, allowing them to save significantly on Azure costs.

---

### **Task 3 – Dependency Analysis**

**1. List the application components involved.**
*   **Servers:** WEB01, WEB02, APP01, SQL01
*   **Load Balancer:** LB01 (Internal)
*   **Database:** SQL Server 2017 on SQL01

**2. Identify at least 8 dependencies.**
1.  **WEB01/WEB02 -> APP01:** Port 443 (HTTPS) for API calls.
2.  **APP01 -> SQL01:** Port 1433 (TCP) for database connectivity.
3.  **LB01 -> WEB01/WEB02:** Health probe on Port 80 (HTTP) or 443 (HTTPS).
4.  **APP01 -> SQL01:** Port 445 (SMB) for any file-based resources or logging.
5.  **WEB01/WEB02 -> APP01:** Port 445 (SMB) for shared content or configuration files.
6.  **All Servers -> Internal Domain Controller (DC):** Ports 389/636 (LDAP/LDAPS), 88 (Kerberos), 53 (DNS).
7.  **All Servers -> Internal NTP/Time Server:** Port 123 (UDP).
8.  **Backup Server -> All Servers:** Port 135 (DCOM) and 445 (SMB) for nightly backups.

**3. Identify which items must be filtered out as noise.**
*   Dependencies to the **backup server** (outside the migration scope).
*   Frequent, short-lived connections to public Microsoft Update endpoints.
*   Internal management/monitoring system connections not part of the application tier.

**4. Document the business requirements.**
*   **Business Criticality:** Tier 1 - Critical to business operations.
*   **Uptime & Downtime Requirements:** Strict 1-hour downtime window for the migration. Post-migration, requires 99.9% uptime SLA.
*   **Data Classification:** Contains PII (Personally Identifiable Information) and is subject to data sovereignty rules.
*   **Licensing Dependencies:** Existing Windows Server and SQL Server 2017 licenses. Desire to use Azure Hybrid Benefit.
*   **Patching Requirements:** Monthly security patching with a 14-day deployment cycle, requiring pre-approved maintenance windows.
*   **Firewall & IP Considerations:** Application requires specific, whitelisted source IPs for access. Prefer static IPs or Azure Load Balancer with floating IP (Direct Server Return) configuration.

---

### **Task 4 – Validate Assessment Results with Application Owner**

**1. Identify 3 recommended VM sizes for WEB/APP/SQL servers.**
*   **WEB01/WEB02:** **D2s_v3** (2 vCPU, 8 GiB RAM) - Sufficient for web server role.
*   **APP01:** **D4s_v3** (4 vCPU, 16 GiB RAM) - More CPU/memory for application logic.
*   **SQL01:** **E4s_v3** (4 vCPU, 32 GiB RAM) - Memory-optimized for SQL Server performance.

**2. Identify what components might need replacement or optimization in Azure.**
*   **Load Balancer (LB01):** Will be replaced with an **Azure Load Balancer** (internal) or an **Azure Application Gateway** for Layer 7 features.
*   **SQL Server 2017:** While it can be rehosted as an Azure VM, this is an opportunity to optimize by considering **Azure SQL Managed Instance** for reduced management overhead.
*   **Clustering/High Availability:** The current setup is not specified, but in Azure, we should implement **Availability Sets** or **Availability Zones** for the WEB/APP tiers and a **SQL Server Always On Availability Group** for the database tier.

**3. Validate whether all dependencies are accounted for.**
*   Yes, the dependency map confirms the 3-tier flow: WEB -> APP -> SQL. All necessary ports and protocols have been documented for Azure NSG and firewall rule creation.

**4. Confirm if SLA and downtime requirements are met.**
*   Using the recommended VMs in an Availability Set configuration provides a 99.95% SLA for the VM infrastructure. Combined with Azure SQL DB's 99.99% SLA, the overall application SLA will meet or exceed the 99.9% requirement. The migration will be performed within the 1-hour downtime window using tools like Azure Site Recovery.

**5. Summarize SQL migration options.**
*   **Rehost (Lift-and-Shift):** Migrate SQL01 to an Azure IaaS VM. **Fastest migration, minimal changes.** Best if the app requires full OS-level access or uses unsupported features.
*   **Azure SQL Managed Instance (MI):** PaaS service that is nearly 100% compatible with SQL Server. **Best for this scenario** as it offers easy lift-and-shift with minimal management, built-in high availability, and patching.
*   **Azure SQL Database:** Fully managed PaaS. **Requires modernization effort** (database compatibility level, feature parity check). Not ideal for a first migration if the application is complex.

---

### **Task 5 – Create the Migration Plan (Documenting the Steps)**

**Pre-migration Tasks:**
1.  Create the target Azure Resource Group and virtual network (VNet) with subnets for WEB, APP, and DB tiers.
2.  Deploy and configure Azure Load Balancer.
3.  Prepare Azure SQL Managed Instance.
4.  Pre-create Network Security Groups (NSGs) based on the dependency analysis.
5.  Rehearse the migration process in a test environment.
6.  Procure and configure a Azure ExpressRoute.

**Migration Steps (per server group):**
1.  **Stop all new user sessions.** Drain connections from the load balancer.
2.  **Wave 1 - WEB Tier:**
    *   Use Azure Site Recovery (ASR) to replicate and migrate WEB01 and WEB02 to Azure.
    *   Place them in an Availability Set behind the Azure Load Balancer.
3.  **Wave 2 - APP Tier:**
    *   Migrate APP01 using ASR.
    *   Update configuration on APP01 to point to the new SQL endpoint.
4.  **Wave 3 - SQL Tier:**
    *  Use Azure Database Migration Service (DMS) for a minimal-downtime migration, performing a final cutover.

**DNS updates:**
*   Plan to reduce TTL on relevant internal DNS records befor starting the migration.
*   Post-migration, update the DNS A records to point to the Azure Load Balancer's frontend IP.

**Connection string changes:**
*   The connection string in APP01 will be updated to point to the new SQL Server endpoint.

**Load balancer considerations:**
*   Configure health probes and load balancing rules on the Azure Load Balancer to mirror the on-premises behavior for the WEB tier.

**SQL migration considerations:**
*   Ensure all SQL Server features used by the application are supported.
*   Perform a final transaction log backup and restore to minimize data loss during the 1-hour cutover.

**Post-migration Validation Checklist:**
- [ ] Can users access the application via its public/private endpoint?
- [ ] Does the load balancer health probe show all WEB VMs as healthy?
- [ ] Are API calls from the WEB tier to the APP tier successful?
- [ ] Can the APP tier connect to and query the SQL database?
- [ ] Is user authentication and authorization working correctly?
- [ ] Are all nightly batch processes/jobs functioning?
- [ ] Validate performance with a synthetic transaction.

**Back-out plan (if the migration fails):**
1.  If critical failure occurs during the 1-hour window, initiate rollback.
2.  **Immediate Rollback:** Re-configure the on-premises load balancer (LB01) to point back to the on-premises WEB servers. Re-point the APP01 server to the on-premises SQL01.
3.  This will restore service to the on-premises environment. The Azure environment will be investigated later to resolve issues before rescheduling the migration.

---

### **Task 6 – Plan the Migration Waves**

**Justification for Grouping:**
*   **Wave 1 (WEB):** Stateless servers. They can be migrated independently and even serve as a pilot. Easy to rollback by simply re-pointing the DNS/LB.
*   **Wave 2 (APP):** Has a dependency on the database. It must be migrated after the WEB tier is stable but before the final database cutover to allow for connection testing with the new SQL environment.
*   **Wave 3 (SQL):** The stateful, most critical component with the highest risk. It is migrated last to minimize the application downtime window. This wave requires the most validation and has the most complex rollback procedure.

**Final Wave Table:**

| Wave    | Servers         | Reason                                                                                                                               |
| :------ | :-------------- | :----------------------------------------------------------------------------------------------------------------------------------- |
| **Wave 1** | WEB01, WEB02    | Stateless front-end servers. Low risk, easy to validate and rollback. Allows for testing the Azure networking and load balancer setup. |
| **Wave 2** | APP01           | Application logic server. Depends on a stable WEB tier and must be reconfigured to connect to the new SQL endpoint in Azure.             |
| **Wave 3** | SQL01           | The core data layer. Highest risk and complexity. Requires a precise cutover to minimize downtime and ensure data integrity.           |

---

### **References**  

*   (https://learn.microsoft.com/en-us/azure/migrate/)
*   (https://learn.microsoft.com/en-us/azure/migrate/create-manage-projects)
*   (https://learn.microsoft.com/en-us/azure/dms/dms-overview)
*   (https://learn.microsoft.com/en-us/azure/migrate/tutorial-assess-vmware-azure-vm)
*   (https://learn.microsoft.com/en-us/azure/migrate/migrate-support-matrix-vmware-migration)
