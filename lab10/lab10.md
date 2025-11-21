
### **Lab Solution: Zero Trust Azure Landing Zone for CloudMed Solutions**



### **1. Company Overview**

**What does CloudMed Solutions do?**
CloudMed Solutions is a healthcare tech company. Their main product, MedConnect, is like a super-secure FaceTime for doctors and patients, plus it manages patient medical records and uses AI to analyze health data.

**Where do they operate?**
They have customers in Canada, the USA, and Europe, so they need to use Azure data centers in **Canada Central** and **West Europe**.

**Why do they NEED a Zero Trust Architecture?**
We can think of it this way: instead of building a strong castle wall and trusting everyone inside (the old way), Zero Trust assumes that there are *already* attackers inside and outside. So, it **verifies every single request** as if it's coming from an untrusted network.

*   **The Drivers:**
    *   **Compliance:** They handle super-sensitive health data. Laws like HIPAA (US), GDPR (Europe), and PIPEDA (Canada) require them to protect this data fiercely. Zero Trust helps them check all the compliance boxes.
    *   **Security:** A data breach would be a disaster. Zero Trust minimizes the damage if someone's credentials get stolen by locking down access and segmenting the network.

---

### **2. Governance and Identity**

**a) Management Group Hierarchy**
This is how we organize our Azure resources neatly. It lets us apply rules and access controls from the top down.

```
Tenant Root
└── CloudMed-Root
    ├── Platform         (Shared security & networking stuff)
    ├── Production       (Live, patient-facing environments)
    └── Development      (Where developers build and test)

```

**b) Governance Model**
*   **RBAC:** Give people the *least privilege* they need to do their job.
    *   **Adins:** Get Owner or Contributor on the Platform group. Powerful, but rarely used.
    *   **DevOps:** Get Contributor on the Dev/Prod groups to deploy apps, but not on the Platform group.
    *   **Finance:** Get Billing Reader on the Root to see costs, but can't change anything.
*   **Azure Policy:**
    *   **Allowed Locations:** Force everyone to only create resources in Canada Central or West Europe.
    *   **Enforce Tagging:** Make sure every resource has tags like Cost-Center and Data-Classification=Confidential. This is crucial for cost tracking and security.
*   **Identity with Azure Entra ID:**
    *   This is our main identity service (formerly Azure Active Directory).
    *   **MFA (Multi-Factor Authentication) is REQUIRED for everyone.** No MFA, no access.
    *   **Conditional Access:** Extra rules like, "If someone is logging in from an unknown country, block them" or "Only allow access from company-managed devices."

---

### **3. Network Architecture (The Secure Road System)**

We're using a **Hub-and-Spoke** model. Imagine the Hub is a secure, fortified city center. The Spokes are different, gated neighborhoods connected to it.

**Hub VNet:**
*   **Azure Firewall:** The main gatekeeper. All traffic going in or out between spokes, or to the internet, is inspected here.
*   **Azure Bastion:** The secure secret tunnel. Admins can use this to log into VMs without exposing them to the public internet. No public IPs needed!
*   **Azure DNS:** Manages private domain names.
*   **Log Analytics Workspace:** Collects all the network logs.

**Spoke VNets:**
Each tier of the application gets its own spoke. This is **segmentation** in action.
*   **App Tier Spoke:** For the web and mobile front-ends.
*   **API Tier Spoke:** For the backend services.
*   **Data Tier Spoke:** For the databases and storage.

**How it aligns with Zero Trust:**
*   **East-West Traffic Control:** By default, the App Tier *cannot* talk directly to the Data Tier. All traffic must go through the Hub Firewall, which can allow or deny it based on strict rules. This stops lateral movement if a breach happens.
*   **Private Endpoints:** Instead of having a public IP address, our Azure SQL Database and Storage will use Private Endpoints. This means they are only accessible from inside our Azure virtual network, invisible to the public internet.

---

### **4. Zero Trust Controls**

Here’s how we apply the three core principles into our design:

1.  **Verify Explicitly:**
    *   **MFA & Conditional Access:** Every login requires multiple proofs of identity and is checked against policies (device, location).
    *   **Design Example:** A doctor can only access the patient management portal from their registered clinic laptop.

2.  **Use Least Privilege Access:**
    *   **Just-in-Time (JIT) VM Access:** Instead of having admin ports open 24/7, DevOps users must request access, which is granted for a short, specific time.
    *   **Design Example:** Using fine-grained RBAC so a developer in the "App Tier" team has no permissions to even see the "Data Tier" resources.

3.  **Assume Breach:**
    *   **Network Segmentation:** The Hub-and-Spoke with the firewall means if the web server (App Tier) is hacked, the attacker is contained there and can't easily reach the database.
    *   **Encrypted Data Only:** All data, whether "at rest" (in a database) or "in transit" (moving between tiers), it should be encrypted by default.
    *   **Design Example:** Using **Azure Policy to deny the creation of any resource with a public IP address**, forcing the use of Private Endpoints and Bastion.

---

### **5. Monitoring, Compliance, and Cost**

*   **Monitoring & Security:**
    *   **Microsoft Defender for Cloud:** Our security dashboard. It constantly scans the environment for misconfigurations and threats and gives a secure score.
    *   **Log Analytics:**  We send all activity logs, firewall logs, and VM logs here for analysis.

*   **Compliance:**
    *   **Azure Policy:** We assign built-in policies for standards like HIPAA and GDPR. It automatically checks our resources and marks them as compliant or non-compliant.
    *   **Compliance Dashboard:** We get a nice report in the Azure portal showing our compliance status against these standards.

*   **Cost Management:**
    *   **Budgets:** We set monthly spending budgets for the Development and Production groups and get alerts if we're going over.
    *   **Tags:** We use the Cost-Center tag to charge back costs to the right department.
    *   **Cost Analysis:** We use the Azure Cost Management tool to see exactly where our money is going.

---

### **6. Conceptual Diagram**

Here's a simple text-based diagram of the architecture. For your actual submission, you should create a visual one using draw.io, Lucidchart, or even PowerPoint, and include it as an image.

```
+-------------------------------------------------------------------------------------------+
|                                CLOUDMED AZURE TENANT                                      |
|                                                                                           |
|  +-----------------------------+                                                          |
|  |   MANAGEMENT GROUPS         |                                                          |
|  |                             |                                                          |
|  |  +----------------------+   |   +--------------+          +--------------+             |
|  |  |   CloudMed-Root      |---+-->| AZURE POLICY |--------->| AZURE ENTRA  |             |
|  |  |                      |   |   | & RBAC       |          | ID (MFA, CA) |             |
|  |  +----------------------+   |   +--------------+          +--------------+             |
|  |    |         |          |   |                                                          |
|  |    |         |          |   |                                                          |
|  | Platform Production  Development |                                                      |
|  +-----------------------------+                                                          |
+-------------------------------------------------------------------------------------------+

+-------------------------------------------------------------------------------------------+
|                                NETWORK LANDING ZONE                                       |
|                                                                                           |
|                                  [ Hub VNet ]                                             |
|              +-----------------------------------------------------------+                |
|              | [ Azure Firewall ]  [ Bastion ]  [ Log Analytics ]        |                |
|              +-----------------------------------------------------------+                |
|                         ^                  ^                  ^                          |
|                         |                  |                  |                          |
|            +------------+                  |                  +------------+             |
|            |                               |                               |             |
|            v                               v                               v             |
|    [ App Tier Spoke ]              [ API Tier Spoke ]              [ Data Tier Spoke ]   |
|    +-----------------+              +----------------+              +-----------------+   |
|    | Web Subnet      |              | API Subnet     |              | SQL Subnet      |   |
|    | (No Public IPs) | <----------> | (No Public IPs)| <--(Private) | (Private Link)  |   |
|    +-----------------+   (Firewall) +----------------+    Endpoint  +-----------------+   |
|                                                                                           |
+-------------------------------------------------------------------------------------------+
|                                                                                           |
|  +-------------------------------------------------------------------------------------+  |
|  |                          ZERO TRUST CONTROLS                                        |  |
|  |                                                                                     |  |
|  |  Assume Breach: Network Segmentation & Private Endpoints                            |  |
|  |  Least Privilege: JIT Access & RBAC                                                 |  |
|  |  Verify Explicitly: MFA & Conditional Access at Entra ID                            |  |
|  +-------------------------------------------------------------------------------------+  |
+-------------------------------------------------------------------------------------------+
```

---

### **7. Summary and Recommendations**

**Summary:**
This design creates a secure, compliant, and scalable foundation for CloudMed in Azure. By using a hub-and-spoke network, strict identity controls, and automated governance, we've directly applied Zero Trust principles. This protects sensitive patient data, meets legal requirements, and gives CloudMed a solid base to grow on.

**Future Recommendations:**

1.  **Automating Everything with DevOps:** Use Infrastructure as Code (IaC) with Terraform or Azure Bicep. This means you can define your entire environment in code files, making it repeatable, less prone to human error, and easy to version control.
2.  **Exploring Cost Optimization:** As usage grows, look into Azure Savings Plans for committed compute spend and Azure Spot VMs for non-critical batch processing jobs (like the AI health analytics) to significantly reduce costs.
