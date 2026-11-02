# Synthetic company: International Corp Inc. (ICI)

All JudgeWorks demos and tests run on a **fictional company** and its **fictional HR system**. No real people, employers or vendor products are represented.

## The company

| Attribute | Value (draft) |
|---|---|
| Name | International Corp Inc. (ICI) |
| Size | about 1,200 employees |
| Locations | US headquarters, plus at least one EU site, so the EU AI Act and GDPR apply |
| Workforce mix | Salaried and hourly, full- and part-time, some union-represented hourly staff, contractors |
| Pay | Biweekly (hourly) and semi-monthly (salaried) pay cycles |

## The HR system: IHRS (ICI HR System)

A home-built HRIS, invented for this project. It uses common HRIS concepts so the patterns carry over to real systems, without copying any vendor's design.

**Draft core tables:**

| Table | What it holds | Key idea |
|---|---|---|
| `person` | Identity: name, birth date, national ID (synthetic) | Restricted data |
| `employment` | Hire, rehire and termination dates per person | One person can have several employment records |
| `job` | Position, department, location, manager, grade, standard hours, status | **Effective-dated:** each change adds a row with `effective_date` and `sequence` |
| `pay_rate` | Salary or hourly rate, currency, frequency | Effective-dated |
| `pay_run` / `pay_line` | Each pay run's earnings, deductions and taxes per employee | Compared run to run by Payroll Check |
| `time_punch` | Clock in and out | Missing and odd punches are planted |
| `leave_balance` / `leave_request` | Accruals, usage, approvals | Balance problems are planted |
| `audit_log` | Who changed what, when | **Correct History** edits change past rows, the case History Watch catches |

**Planted problems:** the generator deliberately adds known issues, such as back-dated job corrections, pay jumps without a job change, missing punches and negative leave balances. The evals can then check that agents find exactly those and nothing else.

**Status:** schema draft. The generator is planned for Python, with a fixed random seed so every run produces the same data.
