# Prestige SQL Backend Repository

## Overview

This repository is the **source of truth** for Prestige’s SQL Server backend code, including:

- Stored procedures
- Data loaders
- Change-history tables
- Correlated-event helper views
- Alerting and scoring logic

The code in this repository supports **event-driven processing**, **security alerts**, and **reporting pipelines**.  
It intentionally separates **change history** from **current state** and avoids reconstructing state where that would be misleading (e.g., Direct Deposit).

> 📌 From the date this repository was created forward, **GitHub is the authoritative source** for all production SQL code managed by the Reporting / Data team.

---

## Design Principles

This repository follows these core principles:

### 1. Event-Based Data Model
- Changes are treated as **immutable events**
- History tables are **append-only**
- Alerting logic anchors on events, not inferred state

### 2. Idempotent & Restart-Safe Logic
- Stored procedures use **data-driven watermarks**
- No logic relies on “last X days” or `GETDATE()` offsets
- Jobs can safely resume after failures without data gaps

### 3. One Object = One File
- Each database object lives in its own `.sql` file
- Files use `CREATE OR ALTER`
- Code in Git should always match what is deployed

### 4. Git Is for Control, Not Storage
- GitHub holds **active and supported logic**
- Historical or superseded logic may live in `_history` folders
- OneDrive is treated as an archive, not an execution source

---

## Repository Structure

prestige-sql-backend/
│
├── tables/
│
├── views/
│
├── stored_procedures/
│   ├── loaders/
│   ├── alerts/
│   └── utilities/
│
├── scripts/
│   ├── backfill/
│   └── maintenance/
│
└── README.md

---

# What Belongs in This Repo

✅ **Include**
- Tables, views, and stored procedures
- ETL / loader logic
- Alerting and scoring procedures
- Backfill and remediation scripts

🚫 **Do Not Include**
- Credentials or connection strings
- SSMS registered servers
- Ad-hoc exploratory queries
- Temporary analysis scripts

---

## Development Workflow

### Making Changes
1. Modify and test in **SSMS**
2. Script the final object using `CREATE OR ALTER`
3. Update the corresponding `.sql` file in this repo
4. Commit with a clear reason-based message

Example commit messages:
- `Refactor DD alerting proc to use data-driven watermark`
- `Add correlated-event helper view for DD alerts`
- `Seed current production version of email history loader`

### Branching
- `main` — production-ready
- `feature/<short-description>` — development

---

## Scheduling & Automation

These stored procedures are executed via:
- Jenkins
- Power Automate
- Manual execution (for backfills)

All scheduled procedures must:
- Be restart-safe
- Avoid hard-coded date logic
- Use data-driven progress tracking

---

## Backfills & Historical Data

Backfills are:
- Explicit
- Scripted
- Isolated from standard loaders

Backfill scripts live in:

scripts/backfill/

---

## Ownership

- **Owner:** Reporting / Data Team
- **Maintainer:** Megan Barnason

---

## Final Notes

This repository shifts backend SQL development toward:

✅ Version control  
✅ Operational safety  
✅ Transparent change history  
✅ Easier recovery from failures  

Clarity and control matter more than perfection.
