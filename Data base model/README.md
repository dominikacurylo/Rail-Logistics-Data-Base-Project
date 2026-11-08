# Logistics on Rails — Relational Database Management System (BDDAD)

![Oracle](https://img.shields.io/badge/Oracle-F80000?style=for-the-badge&logo=oracle&logoColor=white)
![PL/SQL](https://img.shields.io/badge/PL%2FSQL-4479A1?style=for-the-badge&logo=oracle&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-DDL%20%7C%20DML-blue?style=for-the-badge)
![Visual Paradigm](https://img.shields.io/badge/Visual_Paradigm-Data_Modeling-orange?style=for-the-badge)
![Agile SCRUM](https://img.shields.io/badge/Methodology-SCRUM%20(3%20Sprints)-brightgreen?style=for-the-badge)
![Erasmus+](https://img.shields.io/badge/Academic-Erasmus%2B%20%7C%20ISEP%20Porto-blueviolet?style=for-the-badge)

---

## 📌 Executive Summary & Academic Context

This repository contains the complete **Database System module (BDDAD - *Bases de Dados*)** developed for the **"Logistics on Rails"** Integrative Project during an **Erasmus+ exchange programme** at **ISEP (Instituto Superior de Engenharia do Porto, Portugal)** in the 3rd Semester of Informatics Engineering (sem3pi-2025-26).

The broader integrative project was designed to simulate a modern railway logistics operating platform (spanning ARQCP, BDDAD, ESINF, FSIAP, and LAPR3). The **BDDAD** component served as the **central relational information backbone** of the entire system, managing physical railway infrastructure, rolling stock fleets, cargo dispatching, station facilities, line segments, and operational constraints.

The project was executed incrementally over **3 Sprints (4 weeks each)** using an **Agile / SCRUM** team development framework.

---

## 🌟 Leadership & Author Contributions (Dominika Curyło — Student ID: `1250221`)

> **Role:** Team Leader, Database Architect & Core Developer  
> **Repository Commit Share:** **70% of total commits (35 out of 50)**  
> **Student / GitHub Identifier:** `1250221` (`1250221@isep.ipp.pt`)

As the **Team Leader and Lead Database Architect** for team `g144`, I spearheaded the design, implementation, and overall delivery of the database system. In addition to delivering my own user stories, I coordinated the team's workload, supervised the technical direction, maintained repository integrity, and authored the overwhelming majority of the codebase across all three sprints.

### Key Individual Achievements & Responsibilities:

1. **End-to-End Architectural Modeling & Schema Design:**
   - Designed and maintained the entire **Conceptual and Relational Data Models (ERD)** in Visual Paradigm ([`Data Bases project.vpp`](./Data%20Bases%20project.vpp)) from scratch.
   - Handled schema normalisation, defined composite primary keys, foreign keys, and integrity check constraints.
   - Evolved the schema continuously across Sprints 1, 2, and 3 as operational requirements expanded.

2. **DDL Generation & Database Instantiation:**
   - Authored the core physical schema script ([`Model in SQL.txt`](./Model%20in%20SQL.txt)) targeted for Oracle DBMS / Oracle LiveSQL.
   - Ensured strict domain integrity rules (e.g., check constraints for electrification flags, arrival/departure chronological consistency).

3. **Data Engineering & Seeding (DML):**
   - Built comprehensive, realistic seed datasets reflecting Portuguese and European railway networks (Infraestruturas de Portugal / Medway): stations, track segments, sidings, gauges, locomotives (diesel & electric), wagons, and freight manifests ([`Oracle insert code, sprint 1.txt`](./Oracle%20insert%20code,%20sprint%201.txt) and [`Oracle Insert Code - sprint 2.txt`](./Oracle%20Insert%20Code%20-%20sprint%202.txt)).

4. **Team Guidance, Code Review & Conflict Resolution:**
   - Managed repository integration, merged peer contributions, and eliminated schema discrepancies.
   - Refactored and unified Sprint 2 queries into clean, callable PL/SQL stored functions returning `SYS_REFCURSOR` ([`Sprint2_corrections_USBD_as_functions.txt`](./Sprint2_corrections_USBD_as_functions.txt)).
   - Authored and maintained the official project Data Dictionaries and Glossaries across all sprints ([`BDDAD dictionary-glossary.pdf`](./BDDAD%20dictionary-glossary.pdf), [`BDDAD dictionary-glossary - sprint 2.pdf`](./BDDAD%20dictionary-glossary%20-%20sprint%202.pdf), [`dictionary sprint-3.pdf`](./dictionary%20sprint-3.pdf)).

5. **Assigned Sprint 3 User Stories (Production-Grade PL/SQL):**
   - **[USBD38: Add Gauge](./USBD38%20-%20Dominika%20Curylo.txt):**
     - Developed `AddGauge(p_Name, p_Gauge)` in PL/SQL.
     - Included defensive input sanitation (string trimming, case-insensitive duplicate prevention, positive dimension validation).
     - Standardized return status codes (`'OK'`, `'EXISTS'`, `'INVALID'`, `'DB_ERROR'`).
     - Built an automated anonymous block test harness with formatted console reporting.
   - **[USBD44: Add Segment to Existing Line with Siding](./USBD44%20-%20Dominika%20Curylo.txt):**
     - Implemented `AddSegmentToLine(...)` managing complex multi-table transactional logic.
     - Included foreign key validation, relational junction verification (`Line_Segment`), and optional siding configuration.
     - Enforced **transactional atomicity** using named `SAVEPOINT` and `ROLLBACK` to prevent orphaned records if siding insertion fails.
     - Implemented an end-to-end automated test harness covering both positive paths and multiple negative failure modes (`[PASS]` / `[FAIL]`).

---

## 🏗️ Domain & Problem Description

The database models a large-scale railway logistics ecosystem with the following core business domains:

```
  ┌────────────────────────────────────────────────────────┐
  │                 Railway Infrastructure                 │
  │  Facilities (Stations/Terminals), Lines, Segments,     │
  │  Sidings, Track Gauges, Electrification, Speed Limits  │
  └───────────────────────────┬────────────────────────────┘
                              │
  ┌───────────────────────────┴────────────────────────────┐
  │                 Fleet & Rolling Stock                  │
  │  Locomotives (Diesel/Electric), Wagons (Boxcars,       │
  │  Tanks, Hoppers), Models, Operators, Owners            │
  └───────────────────────────┬────────────────────────────┘
                              │
  ┌───────────────────────────┴────────────────────────────┐
  │               Operations & Freight Logistics           │
  │  Freights, Cargo Loads, Planned Trains, Actual Routes, │
  │  Train-Wagon Assignments, Maximum Length Enforcement   │
  └────────────────────────────────────────────────────────┘
```

- **Physical Network:** Multi-segment lines connecting departure and destination facilities. Line segments define physical track gauge (e.g., standard 1435 mm, Iberian 1668 mm), maximum axle weight (t), length (m), speed limit (km/h), electrification status, and passing sidings (for single-track crossings).
- **Rolling Stock:** Differentiates between locomotives (power, acceleration, voltage/frequency or fuel capacity) and freight wagons (payload, tare, volume, specialized cargo types). Locomotives can support multiple track gauges.
- **Freight Dispatching & Monitoring:** Manages freight consignments, train route planning, real-time crossing events, and dynamic length validation.

---

## 🚀 Sprint-by-Sprint Evolution

| Sprint | Focus Area | Key Database Deliverables | Primary User Stories |
|---|---|---|---|
| **Sprint 1** | Conceptual & Logical Relational Modeling | Conceptual model, ERD in Visual Paradigm, DDL generation, initial dataset insertion, analytical SQL queries | USBD02–USBD04, USBD05–USBD20 (Analytical queries for line segments, gauges, fleet classification) |
| **Sprint 2** | Schema Expansion & Programmatic Access | Model refactoring (silos, warehouses, complex paths), incremental data seeding, PL/SQL functions returning `SYS_REFCURSOR` | USBD21–USBD22 (Schema update & DML), USBD23–USBD30 (Train routes, unused wagons, multi-gauge locomotives) |
| **Sprint 3** | Integrity Constraints & PL/SQL Business Logic | Automated business rules, length constraint database trigger, individual PL/SQL stored procedures with full TDD anonymous test blocks | USBD31–USBD33 (Trigger on train max length), USBD34–USBD45 (Facility, wagon, gauge, line/segment management) |

---

## 📂 Repository Structure & File Guide

Below is a breakdown of how the repository is organized and how to understand each file:

```
Data base model/
│
├── 📊 Data Modeling & Project Files
│   ├── Data Bases project.vpp          # Visual Paradigm master project (Conceptual & Logical ERD diagrams)
│   ├── Data Bases project.vpp.bak_*    # Visual Paradigm incremental version backups
│   ├── Model in SQL.txt                # Complete Oracle DDL schema (CREATE TABLE, PK, FK, CHECK constraints)
│   ├── BDDAD dictionary-glossary.pdf   # Sprint 1 Data Dictionary & domain glossary
│   ├── BDDAD dictionary-glossary - sprint 2.pdf # Sprint 2 updated Data Dictionary
│   └── dictionary sprint-3.pdf         # Sprint 3 comprehensive Data Dictionary
│
├── 📥 Database Seeding & Migration (DML)
│   ├── Oracle insert code, sprint 1.txt # Realistic sample data for Sprint 1 schema
│   └── Oracle Insert Code - sprint 2.txt# Seed data additions for Sprint 2 expanded entities
│
├── 🔍 Sprint 1: Analytical SQL Queries
│   └── SQL - User stories, sprint 1.txt# Planner queries (USBD08, USBD13, line queries, gauge counts, etc.)
│
├── ⚙️ Sprint 2: PL/SQL Functions (SYS_REFCURSOR)
│   ├── Sprint2_corrections_USBD_as_functions.txt # Refactored PL/SQL functions with test blocks (USBD23, 28, 30)
│   ├── USBD23.txt                      # Function to retrieve full ordered segments of a train route
│   ├── USBD28.txt                      # Query / function for multi-gauge locomotives by operator
│   └── USBD30.txt                      # Query / function for facilities with grain silos but no warehouses
│
└── 🧩 Sprint 3: Triggers & Distributed User Stories
    ├── USBD33.txt                      # DB Trigger: trg_check_train_max_length (validates train maximum length)
    ├── USBD35 - Duru Ulusoy.txt        # Function: Register a new facility
    ├── USBD36 - Adrianna Ujma.txt      # Function: Add a building to an existing facility
    ├── USBD37 - Igor Michalski.txt     # Function: Register a new wagon and supported gauges
    ├── USBD38 - Dominika Curylo.txt    # ⭐ Function & Tests: Add a new gauge (Defensive checks, sanitization)
    ├── USBD42 - Igor Michalski.txt     # Function: Register train movements and departure timestamps
    ├── USBD43 - Adrianna Ujma.txt      # Function: Add an electric locomotive model
    ├── USBD44 - Dominika Curylo.txt    # ⭐ Function & Tests: Add segment to line + siding (Transactional savepoints)
    └── USBD45 - Duru Ulusoy.txt        # Function: Add a new railway line
```

---

## 🛠️ Technology Stack & Competencies

- **Database Engine:** Oracle Database (tested on Oracle LiveSQL / SQL Developer).
- **Languages:** SQL (ANSI & Oracle Dialect), PL/SQL.
- **Advanced Database Concepts:**
  - **DDL & Schema Design:** Normalization (3NF), Referential Integrity (`CASCADE`, `RESTRICT`), Composite Keys, `CHECK` constraints.
  - **DML & Advanced Querying:** Complex `JOIN`s, Subqueries, Aggregations, Conditional Logic (`CASE ... WHEN`), String Aggregation (`LISTAGG(...) WITHIN GROUP`).
  - **PL/SQL Development:** Stored Functions, Dynamic Cursors (`SYS_REFCURSOR`), Package-like modularity.
  - **Database Triggers:** `BEFORE INSERT OR UPDATE` row-level triggers enforcing multi-table business constraints.
  - **Transaction Management:** Named `SAVEPOINT`s and conditional `ROLLBACK` for multi-statement data consistency.
  - **Defensive Programming:** Null safety, string trimming, case-insensitive duplicate guards (`UPPER(TRIM(...))`), controlled error code returns.
  - **Testing & Verification:** Anonymous PL/SQL test suites with `DBMS_OUTPUT.PUT_LINE` assertions for continuous verification.
- **Software Engineering Tools:** Visual Paradigm (ERD / UML modeling), Git & GitHub, Agile/SCRUM.

---

## 💻 Installation & Execution Guide

To deploy and test the database in Oracle DBMS (or Oracle LiveSQL / SQL Developer):

### Step 1: Instantiate Schema (DDL)
Run the script to create all tables, primary keys, and foreign keys:
```sql
-- Open and execute in Oracle SQL Developer / LiveSQL:
@Model in SQL.txt;
```

### Step 2: Populate Data (DML)
Seed the database with physical network and rolling stock data:
```sql
@Oracle insert code, sprint 1.txt;
@Oracle Insert Code - sprint 2.txt;
```

### Step 3: Install Triggers & Constraints
Compile the operational constraint trigger:
```sql
@USBD33.txt;
```

### Step 4: Deploy & Test Stored Functions
Compile any desired PL/SQL function and run its anonymous testing block. For example, testing **USBD38** or **USBD44**:
```sql
SET SERVEROUTPUT ON;
@USBD38 - Dominika Curylo.txt;
@USBD44 - Dominika Curylo.txt;
```
*Both files contain self-contained test data setups, test cases, and assertion verifications.*

---

## 📜 Team Attribution & Academic Notice

This project was developed within an academic setting at **Instituto Superior de Engenharia do Porto (ISEP)** by team **`g144`**:
- **Dominika Curyło** (`1250221`) — *Team Leader, Lead Data Modeler & Schema Architect, DDL/DML Author, USBD38, USBD44*
- **Adrianna Ujma** (`1250187`) — *USBD36, USBD43*
- **Duru Ulusoy** (`1250223`) — *USBD35, USBD45*
- **Igor Michalski** (`1250251`) — *USBD37, USBD42*

*Note: This repository specifically highlights the database component (BDDAD) of the integrative project.*
