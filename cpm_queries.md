# CPM Queries Reference

This document collects useful `psql_client` queries for inspecting and verifying data in the Check Point Management Server database (`cpm`). These queries are especially helpful for troubleshooting, auditing, and object validation.

---

## 🔍 Query: Retrieve Object & SIC Name of Security Management Server

```bash
psql_client cpm postgres -c "select name,sicname,ipaddress4,creationtime,lastmodifytime from cpnetworkobject_data where sicname like '%cp_mgmt,%' and not deleted and dlesession=0"
```
Sample output:
```
    name    |            sicname             | ipaddress4 |    creationtime     |    lastmodifytime
------------+--------------------------------+------------+---------------------+---------------------
 Management | cn=cp_mgmt,o=gw-b6041a..bubwce | 10.128.0.4 | 2019-11-05 16:54:18 | 2024-11-04 04:27:00
(1 row)
```

**Best practice**: Keep the host name, object name, ICA FQDN and SIC name of a Check Point Security Management aligned for operational clarity, consistency, ease of management, troubleshooting, and trust relationships. [sk164055](https://support.checkpoint.com/results/sk/sk164055)

### Clarification

---

#### 1. Host Name

The **host name** is the system-level name of the server, configured in **Gaia OS** or the underlying operating system.

#### 2. Object Name

The **object name** is the name assigned to the Security Management Server object in **SmartConsole**.

- It is stored in the management database.
- It is visible in the SmartConsole GUI.

#### 3. ICA FQDN

The **Internal Certificate Authority (ICA)** initialization name is the Fully Qualified Domain Name (FQDN) assigned to the ICA during its setup. [R82 Admin Guide](https://sc1.checkpoint.com/documents/R82/WebAdminGuides/EN/CP_R82_SecurityManagement_AdminGuide/Content/Topics-SECMG/CLI/cp_conf-ca.htm)
- The ICA FQDN is used in certificates issued by the Internal CA and is visible in certificate details.
- The ICA FQDN is used in certificate subjects and is referenced by gateways and clients when validating certificates.
- If you change the host name or object name, the ICA FQDN will not automatically update. You must manually reinitialize the ICA if you want the FQDN to match the new host name.

#### 4. SIC Name

The **SIC** (Secure Internal Communication) name is a unique identifier used for authentication between Check Point components. It is **case-sensitive** and must match **exactly** between communicating components (e.g., Security Management Server and Security Gateway). [sk20905](https://support.checkpoint.com/results/sk/sk20905)

---

#### Do They Need to Match?

It is **not strictly required** that the SIC name, object name, and host name all match.  
However, for **operational clarity** and to avoid confusion, it is a **best practice** to keep them consistent.

- The **critical requirement** is that the **SIC name** used for authentication matches what is expected by the peer component (e.g., Security Gateway).

---

#### Key Points from [sk164055](https://support.checkpoint.com/results/sk/sk164055) and Related SKs

- Changing the **host name** or **object name** may require a **SIC reset and re-initialization**, since the SIC name is derived from the object name at the time of SIC setup.
- If the host name or object name is changed **after** SIC has been established, the SIC name **does not update automatically**.
- A **SIC reset** is required for the new name to take effect.
- Mismatched SIC names between components will cause **authentication failures**, such as:
  > "SIC name does not match" errors

---

#### ✅ Practical Guidance

- **Best Practice:** Keep the **host name**, **object name**, **ICA FQDN**, and **SIC name** aligned for easier management and troubleshooting.
- **Technical Requirement:** The **SIC name must match exactly** between the communicating components.  
