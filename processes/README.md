# HR process catalog

Every HR process JudgeWorks covers: its risk tier, the most autonomy an agent may have, and the data it touches. **Risk tier** follows the EU AI Act. AI that influences employment decisions about people is high-risk under Annex III.

**Autonomy:** A0 inform · A1 draft · A2 act with approval · A3 act and report. **High-risk tiers are capped at A1.**

| Area | Processes | Risk tier | Max autonomy | Data class | Agent ideas |
|---|---|---|---|---|---|
| Workforce planning | Headcount plans, position management, org design, reorgs | Limited | A1 | Confidential | Position vacancy and budget reconciliation |
| Talent acquisition | Requisitions, job posts, sourcing, screening, interviews, offers, background checks | **High** | A1 | Confidential | Job-post drafting, bias check on job ads, interview kit drafting. Never ranks or rejects candidates |
| Onboarding | Pre-hire forms, I-9 and E-Verify, equipment and access, first-week plans | Limited | A2 | Restricted | Onboarding checklist tracker, missing-document reminders |
| **Core HR / HRIS** | Employee and job data, effective-dated changes, Correct History, data quality, integrations | Limited | A0 | Restricted | **History Watch** (built first), data-quality checks |
| **Time and attendance** | Timesheets, schedules, overtime, exceptions | Limited (**High** if used to evaluate people) | A3 for reminders, A1 otherwise | Confidential | **Time and Leave** (built first): missing punches, overtime risk |
| **Leave and absence** | Vacation, sick leave, FMLA and state leave, accommodations | Limited | A1 | Restricted (health) | **Time and Leave**: balance problems, eligibility checklists |
| **Payroll** | Pay runs, pre-release checks, retro pay, garnishments, tax, year-end | Limited (financial controls) | A2 | Restricted | **Payroll Check** (built first): pay-run comparison, anomaly flags |
| Benefits | Enrollment, life events, carrier feeds (EDI 834), ACA reporting | Limited | A1 | Restricted (health) | Life-event checklist, carrier-feed error triage |
| Compensation | Salary structures, merit and bonus cycles, pay equity | **High** for decisions | A1 | Restricted | Pay-equity analysis report, cycle data checks |
| Performance and talent | Goals, reviews, calibration, succession, promotions | **High** | A1 | Confidential | Review-writing aid for managers, calibration data prep |
| Learning | Required training, compliance tracking, skills | Limited | A3 for reminders | Internal | Overdue-training reminders |
| Employee relations | Investigations, case management, discipline, grievances | **High** | A1 | Restricted | Case summary and timeline drafting for investigators |
| **HR services** | Employee questions, policy Q&A, ticket triage, knowledge base | Limited | A1 | Internal to Confidential | **HR Services** (built first): cited answers, triage, handoff |
| Offboarding | Terminations, access removal, final pay, exit interviews | **High** for the decision | A1 for the decision, A2 for the checklist | Restricted | Offboarding checklist, access-removal verification |
| Compliance and reporting | EEO-1, OSHA logs, labor-law changes, records retention, data-subject requests | Limited | A1 | Restricted | Data-subject request (DSAR) collection, retention-rule checks |
| People analytics | Turnover, headcount, diversity, attrition | Limited (**High** if it targets individuals) | A0 | Confidential (aggregated) | Monthly people report with anomaly notes |
| Global mobility | Immigration, relocation, assignments | Limited | A1 | Restricted | Visa expiry tracker |
| Contingent workforce | Contractors, statements of work, vendor management systems | Limited | A1 | Confidential | Contractor tenure and co-employment risk flags |
| Labor relations | Union agreements, grievances | **High** | A1 | Confidential | Contract clause lookup |
| Health and safety | Incidents, OSHA recordkeeping | Limited | A1 | Restricted | Incident report drafting |
| Engagement and recognition | Surveys, recognition programs | Limited | A1 | Confidential (aggregated) | Survey comment themes, never at individual level |

**Prohibited in JudgeWorks** (EU AI Act Article 5 and good practice): emotion recognition of workers, inferring sensitive traits from biometrics, and secret monitoring.
