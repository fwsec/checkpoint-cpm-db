# Check Point CPM & Monitoring Database Schema - R82

This repository documents the internal schema of the Check Point **CPM (Central Policy Management)** and **Monitoring** PostgreSQL databases.

Each section below links to a detailed Markdown file containing table and view structures, sample rows, indexes, and constraints.

---

## Database Structure Overview - R82

- [Monitoring Tables](./monitoring_tables.md)  
- [Monitoring Views](./monitoring_views.md)  
- [CPM Tables](./cpm_tables.md)  
- [CPM Views](./cpm_views.md)

---

## Description

Each document includes:
- Column definitions with data types  
- Indexes, constraints, and triggers  
- Sample data (first 20 rows per table)  

Generated automatically from:
```bash
psql_client monitoring postgres -c "\dt"
psql_client monitoring postgres -c "\dv"
psql_client cpm postgres -c "\dt"
psql_client cpm postgres -c "\dv"
