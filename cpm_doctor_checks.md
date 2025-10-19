# Check Point CPM Doctor Tool – Checks Overview

## 🧰 What is CPM Doctor?
The **CPM Doctor Tool** is a diagnostic utility provided by Check Point to analyze the health and integrity of the **Central Policy Manager (CPM)** database and related components on Check Point Security Management Servers. It is especially useful for identifying issues that may affect **SmartConsole** performance, policy installation, database integrity, and overall management server stability.

## 📍 Location
The CPM Doctor Tool is located on the Check Point Management Server at:
$FWDIR/scripts/cpm_doctor.sh

## 🔄 Keeping It Up to Date

- The **CPM Doctor Tool** is **automatically updated** with every **Jumbo Hotfix (JHF)** installation.
- It is **strongly recommended** to always use the **latest build** of the tool. The **current latest build is 570**.
- If the latest version is not available on your system, you can **request it from Check Point Support** by opening a **Service Request (SR#)**.

## 🕒 When to Run It

- Run the CPM Doctor **regularly** as part of your maintenance routine.
- Always run it **when experiencing issues with SmartConsole**, especially if you notice:
  - Delays or failures in publishing or installing policies
  - Crashes or unresponsiveness in SmartConsole
  - Problems with database synchronization or HA
  - Issues with API access or performance

## 📋 Checks Performed (Build 570)

The following is a categorized list of checks performed by **CPM Doctor (build 570)**:

### ✅ OK Checks
- Group Size Checks
- Duplicated Dynamic IP Check
- Disabled Trusted CAs Check
- Empty SID in AD Users, Groups, Machines
- Core Dumps Check – Collecting Files
- WEB API Login Requests Per Minute
- Interfaces of Gateways or Clusters Contains Empty Netmask
- Number of Revoked Certificates Check
- Enormous Size of AbstractAuditLogBase Table
- IPS Database Size
- Deleted Objects With no Delete Audit Logs
- IPS Protections Overrides Check
- Duplicated IPS Overrides Keys Check
- Missing Automatic NAT Rules Check
- Default Geo Policy Object Missing
- Cluster Checks
- Validate Log server for GWs and Clusters
- Check 'need_local_update' Value
- Deleted Mirror Objects from Gateways and Servers View
- Objects not Purged
- Multiple IPS Silent Packages
- Missing IpsUpdateUserSettings Object
- DLP Related Objects Fwset Check
- VPN Community Group Gateways Check
- Sic Local Certificate Subject Check
- Gateways Missing Applications Check
- Shadow_objects.C Empty Check
- IpsUpdateScheduledEvent Object Missing Check
- gw_policies Group Permission Check
- Inconsistency of exe object check
- IPS Silent Package in DB
- Bad Permissions on Postgres Path Folders
- FWM Ports Check
- Logs Applications Name Check
- Duplication Check in accessctrlrule_data and hitcount Tables
- $MDS_FWDIR/tmp Subfolders Amount Check
- Install_on_targets In Fwset Check
- Internal CA Certificates Doesn't Match CA Count for Domain
- Hyper Threading is disabled
- MDS_TEMPLATE/log Redirection Check
- Un-correlated IPS Protections Check
- Largeobjects that not in internalfile
- IPS Update Package Map Without a worksession
- Discarded worksessions with Non Zero number_of_operations Field
- Tags On Groups Check
- Open WorkSession without ObjectStoreSessionEntity
- Published WorkSession without ObjectStoreSessionEntity
- Private Sessions With Changes With no domainWorkSession
- No Objects Without Folder
- Verify All Folders are Visible
- Check That get_ip_range_of_objid Function is Defined
- Find objects containing _BAD_CHAR_REMOVED_
- Is Purge Used
- Locked Objects on Non-existing worksessions
- Orphan Threat Policies
- SmartView_Background Object Check
- Auto Calculated Networks Check
- Deleted Service groups – Nat Rules Corruption Check
- Disabled Triggers Check
- Broken Links From not Existing GRC Objects
- Deleted Administrators That Still has Opened WEB API Sessions
- Unknown Roles Check
- External Gateways Shared Secret Check
- Duplicate QoS Links Check
- Reserved Scheduled Event Name
- Validation Incidents of Link Constraint that are not Deleted and Public
- IPS Duplicate Protections in Management DataBase
- Domainid and dledomainid in domainworksession Table are Inconsistent
- Threat Profile with Broken Links
- Network Interfaces Consistency Check
- DB Check for Broken Links of Gateways, Clusters and Hosts Objects
- Inconsistent Administrators
- Permissions Folder Duplication Check
- Invalid OPSEC References Links Check
- Session Id with no Corresponding ObjectStoreSessionEntity Check
- Cluster Members Multiple Affiliation Check
- Machines In The Environment Info
- User Application Check
- TLSInspectionPolicy Duplication Check
- Broken RB ELIs Check
- Duplicate InspectLogs in the DB Check
- Gateways with duplicate interface names
- Unavailable objects in Access Policy Rules
- Initiate Login Check
- Network Objects Fwset Size Check
- Appliance or Virtual Check
- BIOS Version Check
- Application Control Data Overload Check
- Internal Certificate Authority Check
- Internal Files Check
- Disk/Memory Statistics
- shadow_objects.C File is Corrupted or Huge
- Revoked Certificates Check
- Profile Modifications Check
- API Usage Check
- Management API Use Test
- Postgres DB Size on File System
- Large cp.contract File Size
- gzip and pigz Files are Missing
- Duplicate or Missing firewall_properties for Each Domain Check
- primary_management Field Check In The file-system
- Invalid network references in license object
- Not empty hosted_by_mds field on SMC
- Domain aggregator upgrade status check
- Duplicate IDs for VPN communities
- Missing Custom IPS Package
- Login Timeout Check
- CPCA Permissions Check
- Heap Size Checks
- CPM Deadlock
- Logs In Debug Mode
- Load Check
- Core Dumps Check
- Logging Stats Server and Gateways
- Big CPM Server Log Files
- License Info
- MGMT WebAPI Process out of Memory
- Postgres Debugging
- Postgres Logging
- Web API Show Package Tool Check
- API show-package Failure Check
- Largest Directories in File System
- Duplicate Admin name
- Duplicate Automatic Purge Settings Check
- Disabled Automatic Purge Settings Check
- Hotfixes Installed on the Machine
- CPM Server Jar Check
- HTTPSi Rules Exist In Legacy HTTPSi Policy Check
- Java Xmx Check
- primary_management Field Check In The file-system
- Missing IpsUpdateInspectFileList

### ℹ️ Informational Checks
- CPM Log analysis
- Sizing information
- Database Statistics
- System Statistics
- Auditlogs dtypes Statistics
- Largest Files in File System
- Info About Open Worksessions
- md5sum of all tables.C files
- cpmEnvVars.conf file
- Gtar Version Check
- Failed Tasks Logs Check – Collecting Files

---

## 📞 Need Help?

If you encounter issues that are not resolved by the CPM Doctor Tool, or if you need the latest build, please contact **Check Point Support** and open a **Service Request (SR#)**.
