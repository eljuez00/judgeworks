# Evals

Automated checks that score whether an agent did the right thing. **Status: planned.**

## Approach

- **Binary pass/fail first.** For example: "Did the payroll check flag the employee whose pay jumped 40% with no job change?" Code checks what it can directly.
- **Judges are pluggable**, for the cases code can't check:
  - an LLM judge running locally
  - a decision model such as TypeSafe AI's Jev, which returns probabilities for typed yes/no, score and choice questions instead of free text
- **Frozen runs:** save agent transcripts and grade them repeatedly, to measure judge consistency.

## First candidates

1. HRIS change audit: every changed job-data field in the synthetic company is reported, with no false alarms
2. HR services Q&A: answers cite the right policy section, and out-of-scope questions are handed to a human
3. Payroll pre-release checks: planted pay anomalies are flagged before release
