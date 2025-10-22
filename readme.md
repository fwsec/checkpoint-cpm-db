# 👨‍💻 Check Point CPM Deep Dive

A technical reference for exploring the internals of Check Point Management's CPM architecture. This repo documents the commands used by the **CPM Doctor Tool**, the structure of the **PostgreSQL databases** (`cpm` and `monitoring`), and provides a collection of useful `psql_client` queries for diagnostics, automation, and reverse engineering.

---

## 📂 Contents

- [`cpm_doctor_checks.md`](https://github.com/fwsec/checkpoint-cpm-db/blob/main/cpm_doctor_checks.md) — Breakdown of CPM Doctor Tool commands and their functions
- [`cpm_db_schema.md`](https://github.com/fwsec/checkpoint-cpm-db/blob/main/cpm_db_schema.md) — PostgreSQL schema mapping for the `cpm` database
- [`cpm_queries.md`](https://github.com/fwsec/checkpoint-cpm-db/blob/main/cpm_queries.md) — Useful PostgreSQL queries

---

## 🎓 What Is CPM?

CPM (Check Point Management) is the backend engine powering SmartConsole and other management interfaces. It relies on a PostgreSQL database to store policy objects, logs, sessions, and monitoring data.

This repo aims to demystify CPM internals for engineers who want to:

- Understand how CPM Doctor diagnoses issues
- Explore the structure of the `cpm` and `monitoring` databases
- Run advanced queries using `psql_client`
- Build tools or scripts for Check Point automation

---

## 🧪 Requirements

To run queries or scripts from this repo, you'll need:

- Access to a Check Point Management Server (R8x)
- System mode access (`expert`)
- `psql_client` installed and working
- Basic familiarity with PostgreSQL

---

## 📚 Resources

Here are some helpful links and references for working with Check Point CPM:

- [Check Point Support Center](https://supportcenter.checkpoint.com)
- [CPM Doctor Tool Overview (sk164578)](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk164578)
- [PostgreSQL Official Documentation](https://www.postgresql.org/docs/)
- [Check Point CLI Reference Guide](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk106853)
- [Check Point CheckMates Community](https://community.checkpoint.com/)

---

## 🤝 Contributing

Contributions are welcome and appreciated!

If you'd like to add helpful PSQL queries or scripts, feel free to share them.

### Ways to contribute:
- Submit a pull request with new documentation or scripts
- Open an issue to suggest improvements or report inaccuracies
- Share your insights or usage examples in the Discussions tab

Please follow standard GitHub etiquette and keep contributions focused on technical clarity, accuracy, and usefulness.

---

## Example Queries

```bash
psql_client cpm postgres -c "SELECT * FROM cpm"
psql_client monitoring postgres -c "SELECT * FROM monitoring"
