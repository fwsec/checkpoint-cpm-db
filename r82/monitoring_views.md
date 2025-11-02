# Database: Monitoring (Views) - R82

## Table of Contents

  * [alive_gateways](#alive_gateways)
  * [accessible_gateways](#accessible_gateways)
  * [blade_update_statuses_view](#blade_update_statuses_view)
  * [identity_awareness_statuses_view](#identity_awareness_statuses_view)
  * [entitlement_view](#entitlement_view)
  * [statuses_view](#statuses_view)
  * [os_available_packages_view](#os_available_packages_view)
  * [os_installed_packages_view](#os_installed_packages_view)
  * [blade_statuses_view](#blade_statuses_view)
  * [entitlement_assets_view](#entitlement_assets_view)
  * [ve_gateway_licenses_view](#ve_gateway_licenses_view)
  * [nsx_gateway_licenses_view](#nsx_gateway_licenses_view)


### alive_gateways

**Schema Definition:**

```sql
                                      View "public.alive_gateways"
     Column     |            Type             | Collation | Nullable | Default | Storage  | Description 
----------------+-----------------------------+-----------+----------+---------+----------+-------------
 update_date    | timestamp without time zone |           |          |         | plain    | 
 obj_id         | text                        |           |          |         | extended | 
 sic_name       | text                        |           |          |         | extended | 
 gateway        | text                        |           |          |         | extended | 
 ip             | text                        |           |          |         | extended | 
 gateway_domain | text                        |           |          |         | extended | 
View definition:
 SELECT gateways.update_date,
    gateways.obj_id,
    gateways.sic_name,
    gateways.gateway,
    gateways.ip,
    gateways.gateway_domain
   FROM gateways
  WHERE (now() - gateways.update_date::timestamp with time zone) < '00:03:00'::interval;

```

### accessible_gateways

**Schema Definition:**

```sql
                                           View "public.accessible_gateways"
            Column             |            Type             | Collation | Nullable | Default | Storage  | Description 
-------------------------------+-----------------------------+-----------+----------+---------+----------+-------------
 update_date                   | timestamp without time zone |           |          |         | plain    | 
 obj_id                        | text                        |           |          |         | extended | 
 amon_overall_state            | text                        |           |          |         | extended | 
 application_error_description | text                        |           |          |         | extended | 
View definition:
 SELECT overall_status.update_date,
    overall_status.obj_id,
    COALESCE(NULLIF(overall_status.amon_overall_state, ''::text), '8'::text) AS amon_overall_state,
    overall_status.application_error_description
   FROM overall_status;

```

**Sample Data (First 20 Rows):**

```text
     update_date     |                 obj_id                 | amon_overall_state | application_error_description 
---------------------+----------------------------------------+--------------------+-------------------------------
 2025-10-16 09:22:56 | {28532496-7421-8C4F-A306-DED808D0B191} | 0                  | 
(1 row)

```

### blade_update_statuses_view

**Schema Definition:**

```sql
                         View "public.blade_update_statuses_view"
         Column          | Type | Collation | Nullable | Default | Storage  | Description 
-------------------------+------+-----------+----------+---------+----------+-------------
 obj_id                  | text |           |          |         | extended | 
 sic_name                | text |           |          |         | extended | 
 gateway_domain          | text |           |          |         | extended | 
 status_blade_name       | text |           |          |         | extended | 
 update_status           | text |           |          |         | extended | 
 update_description      | text |           |          |         | extended | 
 next_update_description | text |           |          |         | extended | 
 update_db_version       | text |           |          |         | extended | 
View definition:
 SELECT alive_gateways.obj_id,
    alive_gateways.sic_name,
    alive_gateways.gateway_domain,
    blade_update_status.status_blade_name,
    blade_update_status.update_status,
    blade_update_status.update_description,
    blade_update_status.next_update_description,
    blade_update_status.update_db_version
   FROM alive_gateways
     LEFT JOIN blade_update_status ON blade_update_status.obj_id = alive_gateways.obj_id
     LEFT JOIN entitlement ON entitlement.blade_name = blade_update_status.status_blade_name AND entitlement.obj_id = alive_gateways.obj_id
     LEFT JOIN accessible_gateways ON accessible_gateways.obj_id = alive_gateways.obj_id
  WHERE (entitlement.is_active = '1'::text OR entitlement.is_active IS NULL) AND accessible_gateways.amon_overall_state::integer < 7 AND blade_update_status.status_blade_name ~~ concat('%', entitlement.blade_name, '%');

```

### identity_awareness_statuses_view

**Schema Definition:**

```sql
                       View "public.identity_awareness_statuses_view"
           Column           | Type | Collation | Nullable | Default | Storage  | Description 
----------------------------+------+-----------+----------+---------+----------+-------------
 obj_id                     | text |           |          |         | extended | 
 sic_name                   | text |           |          |         | extended | 
 gateway_domain             | text |           |          |         | extended | 
 logged_with_agents         | text |           |          |         | extended | 
 logged_with_captive_portal | text |           |          |         | extended | 
 logged_with_ad_query       | text |           |          |         | extended | 
View definition:
 SELECT alive_gateways.obj_id,
    alive_gateways.sic_name,
    alive_gateways.gateway_domain,
    identity_awareness_status.logged_with_agents,
    identity_awareness_status.logged_with_captive_portal,
    identity_awareness_status.logged_with_ad_query
   FROM alive_gateways
     LEFT JOIN identity_awareness_status ON identity_awareness_status.obj_id = alive_gateways.obj_id
     LEFT JOIN entitlement ON entitlement.blade_name = 'Identity Awareness'::text AND entitlement.obj_id = alive_gateways.obj_id
     LEFT JOIN accessible_gateways ON accessible_gateways.obj_id = alive_gateways.obj_id
  WHERE (entitlement.is_active = '1'::text OR entitlement.is_active IS NULL) AND accessible_gateways.amon_overall_state::integer < 7;

```

### entitlement_view

**Schema Definition:**

```sql
                              View "public.entitlement_view"
        Column        |  Type   | Collation | Nullable | Default | Storage  | Description 
----------------------+---------+-----------+----------+---------+----------+-------------
 family               | text    |           |          |         | extended | 
 family_order         | integer |           |          |         | plain    | 
 obj_id               | text    |           |          |         | extended | 
 sic_name             | text    |           |          |         | extended | 
 gateway              | text    |           |          |         | extended | 
 ip                   | text    |           |          |         | extended | 
 gateway_domain       | text    |           |          |         | extended | 
 blade_name           | text    |           |          |         | extended | 
 entitlement_status   | text    |           |          |         | extended | 
 entitlement_severity | integer |           |          |         | plain    | 
 active_license       | text    |           |          |         | extended | 
 exp_date             | text    |           |          |         | extended | 
 is_active            | text    |           |          |         | extended | 
 is_evaluation        | text    |           |          |         | extended | 
 entitlement_info     | text    |           |          |         | extended | 
 quota                | real    |           |          |         | plain    | 
 used_quota           | real    |           |          |         | plain    | 
View definition:
 SELECT blades.family,
    entitlement.family_order,
    alive_gateways.obj_id,
    alive_gateways.sic_name,
    alive_gateways.gateway,
    alive_gateways.ip,
    alive_gateways.gateway_domain,
    entitlement.blade_name,
    status_calc.calc[1] AS entitlement_status,
    status_calc.calc[2]::integer AS entitlement_severity,
    status_calc.calc[3] AS active_license,
        CASE
            WHEN (entitlement.entitlement_status = 'Entitled'::text OR entitlement.entitlement_status = 'Quota Exceeded'::text) AND entitlement.exp_date IS NULL THEN 'Never'::text
            WHEN entitlement.entitlement_status = 'Not Entitled'::text THEN ''::text
            WHEN entitlement.entitlement_status = 'Evaluation'::text THEN concat(to_char(entitlement.exp_date, 'DD/MM/YYYY'::text), ' (Evaluation)')
            ELSE to_char(entitlement.exp_date, 'DD/MM/YYYY'::text)
        END AS exp_date,
        CASE
            WHEN entitlement.is_active = '1'::text THEN 'True'::text
            ELSE 'False'::text
        END AS is_active,
        CASE
            WHEN entitlement.entitlement_status = 'Evaluation'::text THEN 'True'::text
            ELSE 'False'::text
        END AS is_evaluation,
        CASE
            WHEN (entitlement.entitlement_status = 'Entitled'::text OR entitlement.entitlement_status = 'Evaluation'::text) AND entitlement.quota > 0::double precision AND entitlement.is_active = '1'::text THEN concat('Quota: ', entitlement.used_quota, '/', entitlement.quota, ' ', entitlement.entitlement_info)
            WHEN (entitlement.entitlement_status = 'Entitled'::text OR entitlement.entitlement_status = 'Evaluation'::text) AND entitlement.quota = '-1'::integer::double precision AND entitlement.is_active = '1'::text THEN concat('Quota: ', entitlement.used_quota, '/Unlimited ', entitlement.entitlement_info)
            WHEN status_calc.calc[1] = 'About to Expire'::text THEN concat(thresholds.exp_message, entitlement.entitlement_info)
            ELSE entitlement.entitlement_info
        END AS entitlement_info,
    entitlement.quota,
    entitlement.used_quota
   FROM alive_gateways
     LEFT JOIN entitlement ON entitlement.obj_id = alive_gateways.obj_id
     LEFT JOIN blades ON entitlement.blade_name = blades.name
     LEFT JOIN families ON blades.family = families.name
     LEFT JOIN ( SELECT entitlement_1.obj_id,
            entitlement_1.blade_name,
                CASE
                    WHEN (entitlement_1.entitlement_status = ANY (ARRAY['Expired'::text, 'Not Entitled'::text, 'N/A'::text])) AND entitlement_1.is_active = '0'::text THEN '{"Not Used",0}'::text[]
                    WHEN (entitlement_1.entitlement_status = ANY (ARRAY['Expired'::text, 'N/A'::text])) AND entitlement_1.is_active = '1'::text THEN ARRAY[entitlement_1.entitlement_status, '2'::text]
                    WHEN entitlement_1.entitlement_status = 'Not Entitled'::text AND entitlement_1.is_active = '1'::text THEN '{"No License",2}'::text[]
                    WHEN (entitlement_1.entitlement_status = ANY (ARRAY['Entitled'::text, 'Evaluation'::text])) AND entitlement_1.is_active = '0'::text THEN '{Available,0}'::text[]
                    WHEN (entitlement_1.entitlement_status = 'Entitled'::text OR entitlement_1.entitlement_status = 'Evaluation'::text) AND entitlement_1.is_active = '1'::text AND entitlement_1.exp_date IS NOT NULL AND (entitlement_1.exp_date::timestamp with time zone - now()) < thresholds_1.exp_threshold AND entitlement_1.quota > 0::double precision AND thresholds_1.quota_threshold < 1::double precision AND entitlement_1.quota < entitlement_1.used_quota THEN '{"Quota Exceeded",2}'::text[]
                    WHEN entitlement_1.entitlement_status = 'Evaluation'::text AND entitlement_1.is_active = '1'::text AND entitlement_1.exp_date IS NOT NULL AND (entitlement_1.exp_date::timestamp with time zone - now()) < '7 days'::interval THEN ARRAY['About to Expire'::text, '1'::text]
                    WHEN entitlement_1.entitlement_status = 'Entitled'::text AND entitlement_1.is_active = '1'::text AND entitlement_1.exp_date IS NOT NULL AND (entitlement_1.exp_date::timestamp with time zone - now()) < thresholds_1.exp_threshold THEN '{"About to Expire",1}'::text[]
                    WHEN (entitlement_1.entitlement_status = ANY (ARRAY['Entitled'::text, 'Evaluation'::text])) AND entitlement_1.is_active = '1'::text AND entitlement_1.quota > 0::double precision AND thresholds_1.quota_threshold < 1::double precision AND entitlement_1.quota < entitlement_1.used_quota THEN '{"Quota Exceeded",2}'::text[]
                    WHEN (entitlement_1.entitlement_status = ANY (ARRAY['Entitled'::text, 'Evaluation'::text])) AND entitlement_1.is_active = '1'::text AND entitlement_1.quota > 0::double precision AND ((entitlement_1.quota - entitlement_1.used_quota) / entitlement_1.quota) <= thresholds_1.quota_threshold THEN ARRAY['Quota Warning'::text, '1'::text]
                    ELSE ARRAY['Active'::text, '0'::text]
                END AS calc
           FROM entitlement entitlement_1
             LEFT JOIN thresholds thresholds_1 ON entitlement_1.blade_name = thresholds_1.blade_name) status_calc ON entitlement.obj_id = status_calc.obj_id AND entitlement.blade_name = status_calc.blade_name
     LEFT JOIN accessible_gateways ON accessible_gateways.obj_id = alive_gateways.obj_id
     LEFT JOIN thresholds ON entitlement.blade_name = thresholds.blade_name
  WHERE accessible_gateways.amon_overall_state::integer < 7;

```

### statuses_view

**Schema Definition:**

```sql
                                            View "public.statuses_view"
                    Column                     |  Type   | Collation | Nullable | Default | Storage  | Description 
-----------------------------------------------+---------+-----------+----------+---------+----------+-------------
 obj_id                                        | text    |           |          |         | extended | 
 sic_name                                      | text    |           |          |         | extended | 
 gateway_domain                                | text    |           |          |         | extended | 
 os_system_time                                | text    |           |          |         | extended | 
 os_system_start_time                          | text    |           |          |         | extended | 
 sec_diff_from_mgmt                            | text    |           |          |         | extended | 
 cpu_usage                                     | text    |           |          |         | extended | 
 disk_free_total_space                         | text    |           |          |         | extended | 
 disk_total_space                              | text    |           |          |         | extended | 
 free_real_mem                                 | text    |           |          |         | extended | 
 total_real_mem                                | text    |           |          |         | extended | 
 available_recommended_packages                | text    |           |          |         | extended | 
 connection_to_updates_servers                 | text    |           |          |         | extended | 
 last_dc_update                                | text    |           |          |         | extended | 
 last_dc_connection_status                     | integer |           |          |         | plain    | 
 last_da_update                                | text    |           |          |         | extended | 
 aging_state                                   | integer |           |          |         | plain    | 
 installed_jumbo                               | text    |           |          |         | extended | 
 installed_jumbo_take                          | integer |           |          |         | plain    | 
 recommended_jumbo_take                        | integer |           |          |         | plain    | 
 cp_shared_version                             | text    |           |          |         | extended | 
 cp_shared_build_num                           | text    |           |          |         | extended | 
 fw_accepted_bytes_total_rate                  | text    |           |          |         | extended | 
 fw_accepted_total_rate                        | text    |           |          |         | extended | 
 fw_dropped_total_rate                         | text    |           |          |         | extended | 
 fw_rejected_total_rate                        | text    |           |          |         | extended | 
 fw_connection_rate                            | text    |           |          |         | extended | 
 fw_policy_name                                | text    |           |          |         | extended | 
 fw_install_time                               | text    |           |          |         | extended | 
 fw_log_handle_rate                            | text    |           |          |         | extended | 
 fw_num_conn                                   | text    |           |          |         | extended | 
 cpv_ipsec_current_active_tunnels              | text    |           |          |         | extended | 
 overall_status                                | text    |           |          |         | extended | 
 amon_overall_state                            | text    |           |          |         | extended | 
 application_error_description                 | text    |           |          |         | extended | 
 ls_indexer_read_logs_delay                    | text    |           |          |         | extended | 
 tls_inspection_deployment_state               | text    |           |          |         | extended | 
 tls_inspection_deployment_success_rate        | integer |           |          |         | plain    | 
 tls_inspection_deployment_predicted_cpu_usage | integer |           |          |         | plain    | 
 tls_inspection_deployment_recommendation      | text    |           |          |         | extended | 
 tls_inspection_deployment_progress            | integer |           |          |         | plain    | 
View definition:
 SELECT alive_gateways.obj_id,
    alive_gateways.sic_name,
    alive_gateways.gateway_domain,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE os_status.os_system_time
        END AS os_system_time,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE os_status.os_system_start_time
        END AS os_system_start_time,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN '0'::text
            ELSE (os_status.os_system_time::bigint::double precision - date_part('epoch'::text, os_status.update_date))::text
        END AS sec_diff_from_mgmt,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE
            CASE
                WHEN os_status.cpu_usage::integer = 65535 THEN ''::text
                ELSE os_status.cpu_usage
            END
        END AS cpu_usage,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE os_status.disk_free_total_space
        END AS disk_free_total_space,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE os_status.disk_total_space
        END AS disk_total_space,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE os_status.free_real_mem
        END AS free_real_mem,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE os_status.total_real_mem
        END AS total_real_mem,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE os_status.available_recommended_packages
        END AS available_recommended_packages,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE os_status.connection_to_updates_servers
        END AS connection_to_updates_servers,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN '0'::text
            ELSE os_status.last_dc_update
        END AS last_dc_update,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN 0
            ELSE os_status.last_dc_connection_status
        END AS last_dc_connection_status,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN '9999999995'::text
            ELSE os_status.last_da_update
        END AS last_da_update,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN 3
            WHEN (now() - to_timestamp(os_status.last_dc_update::bigint::double precision)) > '30 days'::interval AND os_status.aging_state = 0 THEN 3
            WHEN (now() - to_timestamp(os_status.last_dc_update::bigint::double precision)) > '30 days'::interval AND os_status.last_dc_connection_status = 0 AND os_status.aging_state < 2 THEN 3
            ELSE os_status.aging_state
        END AS aging_state,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE os_status.installed_jumbo
        END AS installed_jumbo,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN '-1'::integer
            ELSE os_status.installed_jumbo_take
        END AS installed_jumbo_take,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN '-1'::integer
            ELSE os_status.recommended_jumbo_take
        END AS recommended_jumbo_take,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE os_status.cp_shared_version
        END AS cp_shared_version,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE os_status.cp_shared_build_num
        END AS cp_shared_build_num,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE fw_status.fw_accepted_bytes_total_rate
        END AS fw_accepted_bytes_total_rate,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE fw_status.fw_accepted_total_rate
        END AS fw_accepted_total_rate,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE fw_status.fw_dropped_total_rate
        END AS fw_dropped_total_rate,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE fw_status.fw_rejected_total_rate
        END AS fw_rejected_total_rate,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE fw_status.fw_connection_rate
        END AS fw_connection_rate,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE fw_status.fw_policy_name
        END AS fw_policy_name,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE fw_status.fw_install_time
        END AS fw_install_time,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE fw_status.fw_log_handle_rate
        END AS fw_log_handle_rate,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE fw_status.fw_num_conn
        END AS fw_num_conn,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE fw_status.cpv_ipsec_current_active_tunnels
        END AS cpv_ipsec_current_active_tunnels,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 3 THEN accessible_gateways.amon_overall_state
            WHEN accessible_gateways.amon_overall_state::integer <= 3 AND entitlement_status.max_status = 2 THEN '3'::text
            WHEN accessible_gateways.amon_overall_state::integer = 0 AND entitlement_status.max_status = 1 THEN '1'::text
            ELSE accessible_gateways.amon_overall_state
        END AS overall_status,
    accessible_gateways.amon_overall_state,
    accessible_gateways.application_error_description,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN '0'::text
            ELSE log_server.ls_indexer_read_logs_delay
        END AS ls_indexer_read_logs_delay,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE fw_status.tls_inspection_deployment_state
        END AS tls_inspection_deployment_state,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN 0
            ELSE fw_status.tls_inspection_deployment_success_rate
        END AS tls_inspection_deployment_success_rate,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN 0
            ELSE fw_status.tls_inspection_deployment_predicted_cpu_usage
        END AS tls_inspection_deployment_predicted_cpu_usage,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN ''::text
            ELSE fw_status.tls_inspection_deployment_recommendation
        END AS tls_inspection_deployment_recommendation,
        CASE
            WHEN accessible_gateways.amon_overall_state::integer > 6 THEN 0
            ELSE fw_status.tls_inspection_deployment_progress
        END AS tls_inspection_deployment_progress
   FROM alive_gateways
     LEFT JOIN os_status ON os_status.obj_id = alive_gateways.obj_id
     LEFT JOIN fw_status ON fw_status.obj_id = alive_gateways.obj_id
     LEFT JOIN log_server ON log_server.obj_id = alive_gateways.obj_id
     LEFT JOIN accessible_gateways ON accessible_gateways.obj_id = alive_gateways.obj_id
     LEFT JOIN ( SELECT entitlement_view.obj_id,
            max(entitlement_view.entitlement_severity) AS max_status
           FROM entitlement_view
          WHERE entitlement_view.is_active = 'True'::text
          GROUP BY entitlement_view.obj_id) entitlement_status ON alive_gateways.obj_id = entitlement_status.obj_id;

```

### os_available_packages_view

**Schema Definition:**

```sql
                               View "public.os_available_packages_view"
                Column                | Type | Collation | Nullable | Default | Storage  | Description 
--------------------------------------+------+-----------+----------+---------+----------+-------------
 obj_id                               | text |           |          |         | extended | 
 sic_name                             | text |           |          |         | extended | 
 gateway_domain                       | text |           |          |         | extended | 
 deployment_packages_package_name     | text |           |          |         | extended | 
 deployment_packages_display_name     | text |           |          |         | extended | 
 deployment_packages_category         | text |           |          |         | extended | 
 deployment_packages_status           | text |           |          |         | extended | 
 deployment_packages_release_date     | date |           |          |         | plain    | 
 deployment_packages_take_number      | text |           |          |         | extended | 
 deployment_packages_aging_state      | text |           |          |         | extended | 
 deployment_packages_recommended_flag | text |           |          |         | extended | 
 deployment_packages_install_time     | date |           |          |         | plain    | 
View definition:
 SELECT alive_gateways.obj_id,
    alive_gateways.sic_name,
    alive_gateways.gateway_domain,
    os_available_packages.deployment_packages_package_name,
    os_available_packages.deployment_packages_display_name,
    os_available_packages.deployment_packages_category,
    os_available_packages.deployment_packages_status,
    os_available_packages.deployment_packages_release_date,
    os_available_packages.deployment_packages_take_number,
    os_available_packages.deployment_packages_aging_state,
    os_available_packages.deployment_packages_recommended_flag,
    os_available_packages.deployment_packages_install_time
   FROM alive_gateways
     LEFT JOIN os_available_packages ON os_available_packages.obj_id = alive_gateways.obj_id AND (now() - os_available_packages.update_date::timestamp with time zone) < '00:00:30'::interval;

```

### os_installed_packages_view

**Schema Definition:**

```sql
                               View "public.os_installed_packages_view"
                Column                | Type | Collation | Nullable | Default | Storage  | Description 
--------------------------------------+------+-----------+----------+---------+----------+-------------
 obj_id                               | text |           |          |         | extended | 
 sic_name                             | text |           |          |         | extended | 
 gateway_domain                       | text |           |          |         | extended | 
 deployment_packages_package_name     | text |           |          |         | extended | 
 deployment_packages_display_name     | text |           |          |         | extended | 
 deployment_packages_category         | text |           |          |         | extended | 
 deployment_packages_status           | text |           |          |         | extended | 
 deployment_packages_release_date     | date |           |          |         | plain    | 
 deployment_packages_recommended_flag | text |           |          |         | extended | 
 deployment_packages_install_time     | date |           |          |         | plain    | 
View definition:
 SELECT alive_gateways.obj_id,
    alive_gateways.sic_name,
    alive_gateways.gateway_domain,
    os_available_packages.deployment_packages_package_name,
    os_available_packages.deployment_packages_display_name,
    os_available_packages.deployment_packages_category,
    os_available_packages.deployment_packages_status,
    os_available_packages.deployment_packages_release_date,
    os_available_packages.deployment_packages_recommended_flag,
    os_available_packages.deployment_packages_install_time
   FROM alive_gateways
     LEFT JOIN os_available_packages ON os_available_packages.obj_id = alive_gateways.obj_id AND (now() - os_available_packages.update_date::timestamp with time zone) < '00:00:30'::interval AND os_available_packages.deployment_packages_status = 'Installed'::text;

```

### blade_statuses_view

**Schema Definition:**

```sql
                         View "public.blade_statuses_view"
      Column       | Type | Collation | Nullable | Default | Storage  | Description 
-------------------+------+-----------+----------+---------+----------+-------------
 obj_id            | text |           |          |         | extended | 
 sic_name          | text |           |          |         | extended | 
 gateway_domain    | text |           |          |         | extended | 
 type              | text |           |          |         | extended | 
 status_blade_name | text |           |          |         | extended | 
 status_code       | text |           |          |         | extended | 
 status_short_desc | text |           |          |         | extended | 
 status_long_desc  | text |           |          |         | extended | 
 ha_member_state   | text |           |          |         | extended | 
View definition:
 SELECT alive_gateways.obj_id,
    alive_gateways.sic_name,
    alive_gateways.gateway_domain,
    'Blade Status'::text AS type,
    blade_status.status_blade_name,
    blade_status.status_code,
    blade_status.status_short_desc,
        CASE
            WHEN blade_status.status_long_desc <> ''::text THEN blade_status.status_long_desc
            ELSE blade_status.status_short_desc
        END AS status_long_desc,
    blade_status.ha_member_state
   FROM alive_gateways
     LEFT JOIN accessible_gateways ON accessible_gateways.obj_id = alive_gateways.obj_id
     LEFT JOIN blade_status ON blade_status.obj_id = alive_gateways.obj_id
  WHERE accessible_gateways.amon_overall_state::integer < 7
UNION ALL
 SELECT entitlement_view.obj_id,
    entitlement_view.sic_name,
    entitlement_view.gateway_domain,
    'Entitlement'::text AS type,
    entitlement_view.blade_name AS status_blade_name,
    entitlement_view.entitlement_severity::text AS status_code,
        CASE
            WHEN entitlement_view.entitlement_status = 'About to Expire'::text THEN concat(entitlement_view.entitlement_status, ' on ', entitlement_view.exp_date)
            ELSE entitlement_view.entitlement_status
        END AS status_short_desc,
    concat(
        CASE
            WHEN entitlement_view.entitlement_status = 'About to Expire'::text THEN concat(entitlement_view.entitlement_status, ' on ', entitlement_view.exp_date)
            ELSE entitlement_view.entitlement_status
        END, '. ', entitlement_view.entitlement_info) AS status_long_desc,
    ''::text AS ha_member_state
   FROM entitlement_view
     LEFT JOIN accessible_gateways ON accessible_gateways.obj_id = entitlement_view.obj_id
  WHERE entitlement_view.is_active = 'True'::text AND accessible_gateways.amon_overall_state::integer < 7;

```

### entitlement_assets_view

**Schema Definition:**

```sql
                                View "public.entitlement_assets_view"
             Column              |  Type   | Collation | Nullable | Default | Storage  | Description 
---------------------------------+---------+-----------+----------+---------+----------+-------------
 obj_id                          | text    |           |          |         | extended | 
 sic_name                        | text    |           |          |         | extended | 
 gateway                         | text    |           |          |         | extended | 
 ip                              | text    |           |          |         | extended | 
 gateway_domain                  | text    |           |          |         | extended | 
 account_id                      | text    |           |          |         | extended | 
 pkg_description                 | text    |           |          |         | extended | 
 container_ck                    | text    |           |          |         | extended | 
 container_sku                   | text    |           |          |         | extended | 
 licenses_fwset                  | text    |           |          |         | extended | 
 os_asset_container_ck_signature | text    |           |          |         | extended | 
 support_level                   | text    |           |          |         | extended | 
 activation_status               | text    |           |          |         | extended | 
 support_expiration              | text    |           |          |         | extended | 
 entitlement_severity            | integer |           |          |         | plain    | 
 entitlement_overall_status      | text    |           |          |         | extended | 
 entitlement_overall_status_desc | text    |           |          |         | extended | 
 next_exp_date                   | text    |           |          |         | extended | 
View definition:
 SELECT alive_gateways.obj_id,
    alive_gateways.sic_name,
    alive_gateways.gateway,
    alive_gateways.ip,
    alive_gateways.gateway_domain,
    entitlement_assets.account_id,
    entitlement_assets.pkg_description,
    entitlement_assets.container_ck,
    entitlement_assets.container_sku,
    entitlement_assets.licenses_fwset,
    entitlement_assets.os_asset_container_ck_signature,
    entitlement_assets.support_level,
        CASE
            WHEN entitlement_assets.activation_status = '-1'::text THEN 'N/A'::text
            WHEN entitlement_assets.activation_status = '0'::text THEN 'Not Activated'::text
            WHEN entitlement_assets.activation_status = '1'::text THEN 'Evaluation'::text
            WHEN entitlement_assets.activation_status = '2'::text THEN 'Activated'::text
            WHEN entitlement_assets.activation_status = '3'::text OR entitlement_assets.activation_status = '4'::text THEN 'Not Supported'::text
            ELSE NULL::text
        END AS activation_status,
    to_char(entitlement_assets.support_expiration, 'DD/MM/YYYY'::text) AS support_expiration,
        CASE
            WHEN error_warnings_view.entitlement_severity <> 2 AND entitlement_assets.activation_status = '0'::text THEN 1
            ELSE error_warnings_view.entitlement_severity
        END AS entitlement_severity,
        CASE
            WHEN entitlement_assets.activation_status = '3'::text OR entitlement_assets.activation_status = '4'::text THEN 'Not Supported'::text
            WHEN error_warnings_view.entitlement_severity = 2 THEN 'Error'::text
            WHEN error_warnings_view.entitlement_severity = 1 OR entitlement_assets.activation_status = '0'::text THEN 'Warning'::text
            WHEN error_warnings_view.entitlement_severity = 0 THEN 'OK'::text
            ELSE 'N/A'::text
        END AS entitlement_overall_status,
        CASE
            WHEN entitlement_assets.activation_status = '3'::text THEN 'See VSX object'::text
            WHEN entitlement_assets.activation_status = '4'::text THEN 'Not available for current OS'::text
            WHEN error_warnings_view.entitlement_severity = 2 AND error_warnings_view.number_of_blades = 1 THEN concat('Error with ', error_warnings_view.number_of_blades::text, ' blade')
            WHEN error_warnings_view.entitlement_severity = 2 THEN concat('Errors with ', error_warnings_view.number_of_blades::text, ' blades')
            WHEN entitlement_assets.activation_status = '0'::text THEN 'Not Activated'::text
            WHEN error_warnings_view.entitlement_severity = 1 AND error_warnings_view.number_of_blades = 1 THEN concat('Warning with ', error_warnings_view.number_of_blades::text, ' blade')
            WHEN error_warnings_view.entitlement_severity = 1 THEN concat('Warnings with ', error_warnings_view.number_of_blades::text, ' blades')
            WHEN error_warnings_view.entitlement_severity = 0 THEN 'OK'::text
            ELSE 'N/A'::text
        END AS entitlement_overall_status_desc,
    to_char(exp_date_view.min_date, 'DD/MM/YYYY'::text) AS next_exp_date
   FROM alive_gateways
     LEFT JOIN entitlement_assets ON entitlement_assets.obj_id = alive_gateways.obj_id
     LEFT JOIN ( SELECT t.obj_id,
            t.entitlement_severity,
            count(*) AS number_of_blades
           FROM entitlement_view t
          WHERE t.is_active = 'True'::text AND t.entitlement_severity = (( SELECT max(t1.entitlement_severity) AS max
                   FROM entitlement_view t1
                  WHERE t1.is_active = 'True'::text AND t.obj_id = t1.obj_id))
          GROUP BY t.obj_id, t.entitlement_severity) error_warnings_view ON alive_gateways.obj_id = error_warnings_view.obj_id
     LEFT JOIN ( SELECT entitlement.obj_id,
            min(entitlement.exp_date) AS min_date
           FROM entitlement
          WHERE entitlement.entitlement_status = ANY (ARRAY['Entitled'::text, 'Evaluation'::text])
          GROUP BY entitlement.obj_id) exp_date_view ON alive_gateways.obj_id = exp_date_view.obj_id
     LEFT JOIN accessible_gateways ON accessible_gateways.obj_id = alive_gateways.obj_id
  WHERE accessible_gateways.amon_overall_state::integer < 7;

```

### ve_gateway_licenses_view

**Schema Definition:**

```sql
                          View "public.ve_gateway_licenses_view"
        Column        |  Type   | Collation | Nullable | Default | Storage  | Description 
----------------------+---------+-----------+----------+---------+----------+-------------
 pool_type            | text    |           |          |         | extended | 
 package_blades       | text    |           |          |         | extended | 
 pool_cores           | integer |           |          |         | plain    | 
 pool_available_cores | integer |           |          |         | plain    | 
 ve_gatewayname       | text    |           |          |         | extended | 
 ve_givencores        | integer |           |          |         | plain    | 
 gateway_domain       | text    |           |          |         | extended | 
View definition:
 SELECT license_pool_for_ve.pool_type,
    license_pool_for_ve.package_blades,
    license_pool_for_ve.pool_cores,
    license_pool_for_ve.pool_available_cores,
    vsec_gateway_licenses.gatewayname AS ve_gatewayname,
    vsec_gateway_licenses.givencores AS ve_givencores,
    license_pool_for_ve.domainid AS gateway_domain
   FROM license_pool_for_ve
     LEFT JOIN vsec_gateway_licenses ON license_pool_for_ve.objid = vsec_gateway_licenses.givenpool;

```

### nsx_gateway_licenses_view

**Schema Definition:**

```sql
                               View "public.nsx_gateway_licenses_view"
             Column              |  Type   | Collation | Nullable | Default | Storage  | Description 
---------------------------------+---------+-----------+----------+---------+----------+-------------
 nsx_pool_type                   | text    |           |          |         | extended | 
 nsx_package_blades              | text    |           |          |         | extended | 
 pool_physical_sockets           | integer |           |          |         | plain    | 
 pool_available_physical_sockets | integer |           |          |         | plain    | 
 nsx_gatewayname                 | text    |           |          |         | extended | 
 nsx_givencores                  | integer |           |          |         | plain    | 
 gateway_domain                  | text    |           |          |         | extended | 
View definition:
 SELECT license_pool_for_nsx.nsx_pool_type,
    license_pool_for_nsx.nsx_package_blades,
    license_pool_for_nsx.pool_physical_sockets,
    license_pool_for_nsx.pool_available_physical_sockets,
    vsec_gateway_licenses.gatewayname AS nsx_gatewayname,
    vsec_gateway_licenses.givencores AS nsx_givencores,
    license_pool_for_nsx.domainid AS gateway_domain
   FROM license_pool_for_nsx
     LEFT JOIN vsec_gateway_licenses ON license_pool_for_nsx.objid = vsec_gateway_licenses.givenpool;

```
