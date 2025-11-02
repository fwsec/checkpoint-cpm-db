# Database: Monitoring (Tables) - R82

## Table of Contents

  * [os_status](#os_status)
  * [families](#families)
  * [blades](#blades)
  * [thresholds](#thresholds)
  * [gateways](#gateways)
  * [entitlement](#entitlement)
  * [entitlement_assets](#entitlement_assets)
  * [fw_status](#fw_status)
  * [os_available_packages](#os_available_packages)
  * [overall_status](#overall_status)
  * [blade_status](#blade_status)
  * [blade_update_status](#blade_update_status)
  * [identity_awareness_status](#identity_awareness_status)
  * [log_server](#log_server)
  * [hitcount](#hitcount)
  * [license_pool_for_ve](#license_pool_for_ve)
  * [license_pool_for_nsx](#license_pool_for_nsx)
  * [vsec_gateway_licenses](#vsec_gateway_licenses)


### os_status

**Schema Definition:**

```sql
                                                             Table "public.os_status"
             Column             |            Type             | Collation | Nullable |      Default       | Storage  | Stats target | Description 
--------------------------------+-----------------------------+-----------+----------+--------------------+----------+--------------+-------------
 update_date                    | timestamp without time zone |           |          |                    | plain    |              | 
 obj_id                         | text                        |           | not null |                    | extended |              | 
 os_system_time                 | text                        |           |          |                    | extended |              | 
 os_system_start_time           | text                        |           |          |                    | extended |              | 
 cpu_usage                      | text                        |           |          |                    | extended |              | 
 disk_free_total_space          | text                        |           |          |                    | extended |              | 
 disk_total_space               | text                        |           |          |                    | extended |              | 
 free_real_mem                  | text                        |           |          |                    | extended |              | 
 total_real_mem                 | text                        |           |          |                    | extended |              | 
 available_recommended_packages | text                        |           |          |                    | extended |              | 
 connection_to_updates_servers  | text                        |           |          |                    | extended |              | 
 last_da_update                 | text                        |           |          | '9999999995'::text | extended |              | 
 last_dc_update                 | text                        |           |          | '0'::text          | extended |              | 
 last_dc_connection_status      | integer                     |           |          | 0                  | plain    |              | 
 aging_state                    | integer                     |           |          | 3                  | plain    |              | 
 installed_jumbo                | text                        |           |          | ''::text           | extended |              | 
 installed_jumbo_take           | integer                     |           |          | '-1'::integer      | plain    |              | 
 recommended_jumbo_take         | integer                     |           |          | '-1'::integer      | plain    |              | 
 cp_shared_version              | text                        |           |          |                    | extended |              | 
 cp_shared_build_num            | text                        |           |          |                    | extended |              | 
Indexes:
    "os_status_pkey" PRIMARY KEY, btree (obj_id)
Foreign-key constraints:
    "os_status_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)

```

**Indexes, Constraints, and Triggers:**

```sql
   indexname    |                                  indexdef                                   
----------------+-----------------------------------------------------------------------------
 os_status_pkey | CREATE UNIQUE INDEX os_status_pkey ON public.os_status USING btree (obj_id)
(1 row)

        conname        | contype 
-----------------------+---------
 os_status_pkey        | p
 os_status_obj_id_fkey | f
(2 rows)

```

### families

**Schema Definition:**

```sql
                                Table "public.families"
 Column | Type | Collation | Nullable | Default | Storage  | Stats target | Description 
--------+------+-----------+----------+---------+----------+--------------+-------------
 name   | text |           | not null |         | extended |              | 
Indexes:
    "families_pkey" PRIMARY KEY, btree (name)
Referenced by:
    TABLE "blades" CONSTRAINT "blades_family_fkey" FOREIGN KEY (family) REFERENCES families(name)

```

**Indexes, Constraints, and Triggers:**

```sql
   indexname   |                                indexdef                                 
---------------+-------------------------------------------------------------------------
 families_pkey | CREATE UNIQUE INDEX families_pkey ON public.families USING btree (name)
(1 row)

    conname    | contype 
---------------+---------
 families_pkey | p
(1 row)

```

**Sample Data (First 20 Rows):**

```text
    name    
------------
 Access
 Threat
 DLP
 Management
 General
(5 rows)

```

### blades

**Schema Definition:**

```sql
                                 Table "public.blades"
 Column | Type | Collation | Nullable | Default | Storage  | Stats target | Description 
--------+------+-----------+----------+---------+----------+--------------+-------------
 name   | text |           | not null |         | extended |              | 
 family | text |           |          |         | extended |              | 
Indexes:
    "blades_pkey" PRIMARY KEY, btree (name)
Foreign-key constraints:
    "blades_family_fkey" FOREIGN KEY (family) REFERENCES families(name)

```

**Indexes, Constraints, and Triggers:**

```sql
  indexname  |                              indexdef                               
-------------+---------------------------------------------------------------------
 blades_pkey | CREATE UNIQUE INDEX blades_pkey ON public.blades USING btree (name)
(1 row)

      conname       | contype 
--------------------+---------
 blades_pkey        | p
 blades_family_fkey | f
(2 rows)

```

**Sample Data (First 20 Rows):**

```text
            name            |   family   
----------------------------+------------
 Firewall                   | Access
 Network Policy Management  | Management
 Monitoring                 | Management
 IPSec VPN                  | Access
 Data Loss Prevention       | DLP
 Mobile Access              | Access
 Check Point Capsule        | Access
 Anti-Spam & Email Security | Threat
 Application Control        | Access
 Anti-Bot                   | Threat
 Anti-Virus                 | Threat
 URL Filtering              | Access
 IPS                        | Threat
 Threat Emulation           | Threat
 Threat Emulation Local     | Threat
 Threat Extraction          | Threat
 Zero Phishing              | Threat
 Compliance                 | Management
 SmartEvent                 | Management
 Logging & Status           | Management
(20 rows)

```

### thresholds

**Schema Definition:**

```sql
                                      Table "public.thresholds"
     Column      |   Type   | Collation | Nullable | Default | Storage  | Stats target | Description 
-----------------+----------+-----------+----------+---------+----------+--------------+-------------
 blade_name      | text     |           | not null |         | extended |              | 
 quota_threshold | real     |           |          |         | plain    |              | 
 exp_threshold   | interval |           |          |         | plain    |              | 
 exp_message     | text     |           |          |         | extended |              | 
Indexes:
    "thresholds_pkey" PRIMARY KEY, btree (blade_name)

```

**Indexes, Constraints, and Triggers:**

```sql
    indexname    |                                     indexdef                                      
-----------------+-----------------------------------------------------------------------------------
 thresholds_pkey | CREATE UNIQUE INDEX thresholds_pkey ON public.thresholds USING btree (blade_name)
(1 row)

     conname     | contype 
-----------------+---------
 thresholds_pkey | p
(1 row)

```

### gateways

**Schema Definition:**

```sql
                                                Table "public.gateways"
     Column     |            Type             | Collation | Nullable | Default | Storage  | Stats target | Description 
----------------+-----------------------------+-----------+----------+---------+----------+--------------+-------------
 update_date    | timestamp without time zone |           |          |         | plain    |              | 
 obj_id         | text                        |           | not null |         | extended |              | 
 sic_name       | text                        |           |          |         | extended |              | 
 gateway        | text                        |           | not null |         | extended |              | 
 ip             | text                        |           |          |         | extended |              | 
 gateway_domain | text                        |           |          |         | extended |              | 
Indexes:
    "gateways_pkey" PRIMARY KEY, btree (obj_id)
Referenced by:
    TABLE "blade_status" CONSTRAINT "blade_status_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)
    TABLE "blade_update_status" CONSTRAINT "blade_update_status_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)
    TABLE "entitlement_assets" CONSTRAINT "entitlement_assets_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)
    TABLE "entitlement" CONSTRAINT "entitlement_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)
    TABLE "fw_status" CONSTRAINT "fw_status_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)
    TABLE "identity_awareness_status" CONSTRAINT "identity_awareness_status_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)
    TABLE "log_server" CONSTRAINT "log_server_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)
    TABLE "os_available_packages" CONSTRAINT "os_available_packages_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)
    TABLE "os_status" CONSTRAINT "os_status_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)
    TABLE "overall_status" CONSTRAINT "overall_status_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)

```

**Indexes, Constraints, and Triggers:**

```sql
   indexname   |                                 indexdef                                  
---------------+---------------------------------------------------------------------------
 gateways_pkey | CREATE UNIQUE INDEX gateways_pkey ON public.gateways USING btree (obj_id)
(1 row)

    conname    | contype 
---------------+---------
 gateways_pkey | p
(1 row)

```

### entitlement

**Schema Definition:**

```sql
                                                Table "public.entitlement"
       Column       |            Type             | Collation | Nullable | Default | Storage  | Stats target | Description 
--------------------+-----------------------------+-----------+----------+---------+----------+--------------+-------------
 update_date        | timestamp without time zone |           |          |         | plain    |              | 
 obj_id             | text                        |           | not null |         | extended |              | 
 family_order       | integer                     |           |          |         | plain    |              | 
 blade_name         | text                        |           | not null |         | extended |              | 
 entitlement_status | text                        |           |          |         | extended |              | 
 is_active          | text                        |           |          |         | extended |              | 
 exp_date           | timestamp without time zone |           |          |         | plain    |              | 
 quota              | real                        |           |          |         | plain    |              | 
 used_quota         | real                        |           |          |         | plain    |              | 
 entitlement_info   | text                        |           |          |         | extended |              | 
Indexes:
    "entitlement_pkey" PRIMARY KEY, btree (obj_id, blade_name)
Foreign-key constraints:
    "entitlement_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)

```

**Indexes, Constraints, and Triggers:**

```sql
    indexname     |                                          indexdef                                           
------------------+---------------------------------------------------------------------------------------------
 entitlement_pkey | CREATE UNIQUE INDEX entitlement_pkey ON public.entitlement USING btree (obj_id, blade_name)
(1 row)

         conname         | contype 
-------------------------+---------
 entitlement_pkey        | p
 entitlement_obj_id_fkey | f
(2 rows)

```

### entitlement_assets

**Schema Definition:**

```sql
                                                   Table "public.entitlement_assets"
             Column              |            Type             | Collation | Nullable | Default | Storage  | Stats target | Description 
---------------------------------+-----------------------------+-----------+----------+---------+----------+--------------+-------------
 update_date                     | timestamp without time zone |           |          |         | plain    |              | 
 obj_id                          | text                        |           | not null |         | extended |              | 
 container_sku                   | text                        |           | not null |         | extended |              | 
 container_ck                    | text                        |           |          |         | extended |              | 
 licenses_fwset                  | text                        |           |          |         | extended |              | 
 pkg_description                 | text                        |           |          |         | extended |              | 
 account_id                      | text                        |           |          |         | extended |              | 
 support_level                   | text                        |           |          |         | extended |              | 
 support_expiration              | timestamp without time zone |           |          |         | plain    |              | 
 activation_status               | text                        |           |          |         | extended |              | 
 os_asset_container_ck_signature | text                        |           |          |         | extended |              | 
Indexes:
    "entitlement_assets_pkey" PRIMARY KEY, btree (obj_id)
Foreign-key constraints:
    "entitlement_assets_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)

```

**Indexes, Constraints, and Triggers:**

```sql
        indexname        |                                           indexdef                                            
-------------------------+-----------------------------------------------------------------------------------------------
 entitlement_assets_pkey | CREATE UNIQUE INDEX entitlement_assets_pkey ON public.entitlement_assets USING btree (obj_id)
(1 row)

            conname             | contype 
--------------------------------+---------
 entitlement_assets_pkey        | p
 entitlement_assets_obj_id_fkey | f
(2 rows)

```

### fw_status

**Schema Definition:**

```sql
                                                               Table "public.fw_status"
                    Column                     |            Type             | Collation | Nullable | Default | Storage  | Stats target | Description 
-----------------------------------------------+-----------------------------+-----------+----------+---------+----------+--------------+-------------
 update_date                                   | timestamp without time zone |           |          |         | plain    |              | 
 obj_id                                        | text                        |           | not null |         | extended |              | 
 fw_accepted_bytes_total_rate                  | text                        |           |          |         | extended |              | 
 fw_accepted_total_rate                        | text                        |           |          |         | extended |              | 
 fw_dropped_total_rate                         | text                        |           |          |         | extended |              | 
 fw_rejected_total_rate                        | text                        |           |          |         | extended |              | 
 fw_connection_rate                            | text                        |           |          |         | extended |              | 
 fw_policy_name                                | text                        |           |          |         | extended |              | 
 fw_install_time                               | text                        |           |          |         | extended |              | 
 fw_log_handle_rate                            | text                        |           |          |         | extended |              | 
 fw_num_conn                                   | text                        |           |          |         | extended |              | 
 cpv_ipsec_current_active_tunnels              | text                        |           |          |         | extended |              | 
 tls_inspection_deployment_state               | text                        |           |          |         | extended |              | 
 tls_inspection_deployment_success_rate        | integer                     |           |          |         | plain    |              | 
 tls_inspection_deployment_predicted_cpu_usage | integer                     |           |          |         | plain    |              | 
 tls_inspection_deployment_recommendation      | text                        |           |          |         | extended |              | 
 tls_inspection_deployment_progress            | integer                     |           |          |         | plain    |              | 
Indexes:
    "fw_status_pkey" PRIMARY KEY, btree (obj_id)
Foreign-key constraints:
    "fw_status_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)

```

**Indexes, Constraints, and Triggers:**

```sql
   indexname    |                                  indexdef                                   
----------------+-----------------------------------------------------------------------------
 fw_status_pkey | CREATE UNIQUE INDEX fw_status_pkey ON public.fw_status USING btree (obj_id)
(1 row)

        conname        | contype 
-----------------------+---------
 fw_status_pkey        | p
 fw_status_obj_id_fkey | f
(2 rows)

```

### os_available_packages

**Schema Definition:**

```sql
                                                            Table "public.os_available_packages"
                Column                |            Type             | Collation | Nullable |         Default         | Storage  | Stats target | Description 
--------------------------------------+-----------------------------+-----------+----------+-------------------------+----------+--------------+-------------
 update_date                          | timestamp without time zone |           |          |                         | plain    |              | 
 obj_id                               | text                        |           | not null |                         | extended |              | 
 deployment_packages_package_name     | text                        |           | not null |                         | extended |              | 
 deployment_packages_display_name     | text                        |           |          | ''::text                | extended |              | 
 deployment_packages_category         | text                        |           |          | 'UNKNOWN'::text         | extended |              | 
 deployment_packages_status           | text                        |           |          | ''::text                | extended |              | 
 deployment_packages_release_date     | date                        |           |          |                         | plain    |              | 
 deployment_packages_take_number      | text                        |           |          | ''::text                | extended |              | 
 deployment_packages_aging_state      | text                        |           |          | ''::text                | extended |              | 
 deployment_packages_recommended_flag | text                        |           |          | 'Not Recommended'::text | extended |              | 
 deployment_packages_install_time     | date                        |           |          |                         | plain    |              | 
Indexes:
    "os_available_packages_pkey" PRIMARY KEY, btree (obj_id, deployment_packages_package_name)
Foreign-key constraints:
    "os_available_packages_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)

```

**Indexes, Constraints, and Triggers:**

```sql
         indexname          |                                                               indexdef                                                                
----------------------------+---------------------------------------------------------------------------------------------------------------------------------------
 os_available_packages_pkey | CREATE UNIQUE INDEX os_available_packages_pkey ON public.os_available_packages USING btree (obj_id, deployment_packages_package_name)
(1 row)

              conname              | contype 
-----------------------------------+---------
 os_available_packages_pkey        | p
 os_available_packages_obj_id_fkey | f
(2 rows)

```

### overall_status

**Schema Definition:**

```sql
                                                    Table "public.overall_status"
            Column             |            Type             | Collation | Nullable | Default | Storage  | Stats target | Description 
-------------------------------+-----------------------------+-----------+----------+---------+----------+--------------+-------------
 update_date                   | timestamp without time zone |           |          |         | plain    |              | 
 obj_id                        | text                        |           | not null |         | extended |              | 
 amon_overall_state            | text                        |           |          |         | extended |              | 
 application_error_description | text                        |           |          |         | extended |              | 
Indexes:
    "overall_status_pkey" PRIMARY KEY, btree (obj_id)
Foreign-key constraints:
    "overall_status_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)

```

**Indexes, Constraints, and Triggers:**

```sql
      indexname      |                                       indexdef                                        
---------------------+---------------------------------------------------------------------------------------
 overall_status_pkey | CREATE UNIQUE INDEX overall_status_pkey ON public.overall_status USING btree (obj_id)
(1 row)

          conname           | contype 
----------------------------+---------
 overall_status_pkey        | p
 overall_status_obj_id_fkey | f
(2 rows)

```

### blade_status

**Schema Definition:**

```sql
                                               Table "public.blade_status"
      Column       |            Type             | Collation | Nullable | Default | Storage  | Stats target | Description 
-------------------+-----------------------------+-----------+----------+---------+----------+--------------+-------------
 update_date       | timestamp without time zone |           |          |         | plain    |              | 
 obj_id            | text                        |           | not null |         | extended |              | 
 status_blade_name | text                        |           | not null |         | extended |              | 
 status_code       | text                        |           |          |         | extended |              | 
 status_short_desc | text                        |           |          |         | extended |              | 
 status_long_desc  | text                        |           |          |         | extended |              | 
 ha_member_state   | text                        |           |          |         | extended |              | 
Indexes:
    "blade_status_pkey" PRIMARY KEY, btree (obj_id, status_blade_name)
Foreign-key constraints:
    "blade_status_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)

```

**Indexes, Constraints, and Triggers:**

```sql
     indexname     |                                               indexdef                                               
-------------------+------------------------------------------------------------------------------------------------------
 blade_status_pkey | CREATE UNIQUE INDEX blade_status_pkey ON public.blade_status USING btree (obj_id, status_blade_name)
(1 row)

         conname          | contype 
--------------------------+---------
 blade_status_pkey        | p
 blade_status_obj_id_fkey | f
(2 rows)

```

### blade_update_status

**Schema Definition:**

```sql
                                               Table "public.blade_update_status"
         Column          |            Type             | Collation | Nullable | Default | Storage  | Stats target | Description 
-------------------------+-----------------------------+-----------+----------+---------+----------+--------------+-------------
 update_date             | timestamp without time zone |           |          |         | plain    |              | 
 obj_id                  | text                        |           | not null |         | extended |              | 
 status_blade_name       | text                        |           | not null |         | extended |              | 
 update_status           | text                        |           |          |         | extended |              | 
 update_description      | text                        |           |          |         | extended |              | 
 next_update_description | text                        |           |          |         | extended |              | 
 update_db_version       | text                        |           |          |         | extended |              | 
Indexes:
    "blade_update_status_pkey" PRIMARY KEY, btree (obj_id, status_blade_name)
Foreign-key constraints:
    "blade_update_status_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)

```

**Indexes, Constraints, and Triggers:**

```sql
        indexname         |                                                      indexdef                                                      
--------------------------+--------------------------------------------------------------------------------------------------------------------
 blade_update_status_pkey | CREATE UNIQUE INDEX blade_update_status_pkey ON public.blade_update_status USING btree (obj_id, status_blade_name)
(1 row)

             conname             | contype 
---------------------------------+---------
 blade_update_status_pkey        | p
 blade_update_status_obj_id_fkey | f
(2 rows)

```

### identity_awareness_status

**Schema Definition:**

```sql
                                             Table "public.identity_awareness_status"
           Column           |            Type             | Collation | Nullable | Default | Storage  | Stats target | Description 
----------------------------+-----------------------------+-----------+----------+---------+----------+--------------+-------------
 update_date                | timestamp without time zone |           |          |         | plain    |              | 
 obj_id                     | text                        |           | not null |         | extended |              | 
 logged_with_agents         | text                        |           |          |         | extended |              | 
 logged_with_captive_portal | text                        |           |          |         | extended |              | 
 logged_with_ad_query       | text                        |           |          |         | extended |              | 
Indexes:
    "identity_awareness_status_pkey" PRIMARY KEY, btree (obj_id)
Foreign-key constraints:
    "identity_awareness_status_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)

```

**Indexes, Constraints, and Triggers:**

```sql
           indexname            |                                                  indexdef                                                   
--------------------------------+-------------------------------------------------------------------------------------------------------------
 identity_awareness_status_pkey | CREATE UNIQUE INDEX identity_awareness_status_pkey ON public.identity_awareness_status USING btree (obj_id)
(1 row)

                conname                | contype 
---------------------------------------+---------
 identity_awareness_status_pkey        | p
 identity_awareness_status_obj_id_fkey | f
(2 rows)

```

### log_server

**Schema Definition:**

```sql
                                                     Table "public.log_server"
           Column           |            Type             | Collation | Nullable | Default | Storage  | Stats target | Description 
----------------------------+-----------------------------+-----------+----------+---------+----------+--------------+-------------
 update_date                | timestamp without time zone |           |          |         | plain    |              | 
 obj_id                     | text                        |           | not null |         | extended |              | 
 ls_indexer_read_logs_delay | text                        |           |          |         | extended |              | 
Indexes:
    "log_server_pkey" PRIMARY KEY, btree (obj_id)
Foreign-key constraints:
    "log_server_obj_id_fkey" FOREIGN KEY (obj_id) REFERENCES gateways(obj_id)

```

**Indexes, Constraints, and Triggers:**

```sql
    indexname    |                                   indexdef                                    
-----------------+-------------------------------------------------------------------------------
 log_server_pkey | CREATE UNIQUE INDEX log_server_pkey ON public.log_server USING btree (obj_id)
(1 row)

        conname         | contype 
------------------------+---------
 log_server_pkey        | p
 log_server_obj_id_fkey | f
(2 rows)

```

### hitcount

**Schema Definition:**

```sql
                                              Table "public.hitcount"
   Column    |            Type             | Collation | Nullable | Default | Storage  | Stats target | Description 
-------------+-----------------------------+-----------+----------+---------+----------+--------------+-------------
 time_stamp  | timestamp without time zone |           | not null |         | plain    |              | 
 rule_uid    | text                        |           | not null |         | extended |              | 
 netobj_uid  | text                        |           | not null |         | extended |              | 
 netobj_name | text                        |           |          |         | extended |              | 
 policy_type | text                        |           |          |         | extended |              | 
 hits        | bigint                      |           |          |         | plain    |              | 
 start_date  | timestamp without time zone |           |          |         | plain    |              | 
 end_date    | timestamp without time zone |           |          |         | plain    |              | 
Indexes:
    "hitcount_pkey" PRIMARY KEY, btree (time_stamp, rule_uid, netobj_uid)
    "rule_id_idx" btree (rule_uid)

```

**Indexes, Constraints, and Triggers:**

```sql
   indexname   |                                              indexdef                                               
---------------+-----------------------------------------------------------------------------------------------------
 hitcount_pkey | CREATE UNIQUE INDEX hitcount_pkey ON public.hitcount USING btree (time_stamp, rule_uid, netobj_uid)
 rule_id_idx   | CREATE INDEX rule_id_idx ON public.hitcount USING btree (rule_uid)
(2 rows)

    conname    | contype 
---------------+---------
 hitcount_pkey | p
(1 row)

```

### license_pool_for_ve

**Schema Definition:**

```sql
                                   Table "public.license_pool_for_ve"
        Column        |  Type   | Collation | Nullable | Default | Storage  | Stats target | Description 
----------------------+---------+-----------+----------+---------+----------+--------------+-------------
 pool_type            | text    |           |          |         | extended |              | 
 package_blades       | text    |           |          |         | extended |              | 
 pool_cores           | integer |           |          |         | plain    |              | 
 domainid             | text    |           |          |         | extended |              | 
 pool_available_cores | integer |           |          |         | plain    |              | 
 objid                | uuid    |           |          |         | plain    |              | 

```

### license_pool_for_nsx

**Schema Definition:**

```sql
                                        Table "public.license_pool_for_nsx"
             Column              |  Type   | Collation | Nullable | Default | Storage  | Stats target | Description 
---------------------------------+---------+-----------+----------+---------+----------+--------------+-------------
 nsx_pool_type                   | text    |           |          |         | extended |              | 
 nsx_package_blades              | text    |           |          |         | extended |              | 
 pool_physical_sockets           | integer |           |          |         | plain    |              | 
 domainid                        | text    |           |          |         | extended |              | 
 pool_available_physical_sockets | integer |           |          |         | plain    |              | 
 objid                           | uuid    |           |          |         | plain    |              | 

```

### vsec_gateway_licenses

**Schema Definition:**

```sql
                              Table "public.vsec_gateway_licenses"
   Column    |  Type   | Collation | Nullable | Default | Storage  | Stats target | Description 
-------------+---------+-----------+----------+---------+----------+--------------+-------------
 givencores  | integer |           |          |         | plain    |              | 
 domainid    | text    |           |          |         | extended |              | 
 givenpool   | uuid    |           |          |         | plain    |              | 
 gatewayname | text    |           |          |         | extended |              | 

```


