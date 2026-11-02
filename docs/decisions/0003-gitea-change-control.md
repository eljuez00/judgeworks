# 0003: Local Gitea for HR-owned change control

- **Status:** Proposed
- **Date:** 2026-11-02

## Context

An HR department, not just IT, has to own what its AI agents are allowed to do: the policies they answer from, their prompts, autonomy levels, tool permissions and approvers. Those changes need review, approval and a permanent record. ISO/IEC 42001, NIST AI RMF (the Govern function) and the EU AI Act (record-keeping and human oversight) all expect that evidence.

GitHub hosts the public JudgeWorks project. A company running the harness needs a **private, local** place for its own configuration.

## Decision

A company adopting JudgeWorks runs **Gitea**, a lightweight, self-hosted git service (MIT license), locally as the HR department's control center. The JudgeWorks project itself lives on GitHub and ships the [installation and setup instructions](../../infra/gitea/README.md). No Gitea server is part of the public project.

What the company's Gitea holds:

- **Repos:** `hr-policies` (policy documents agents cite), `agent-config` (prompts, autonomy levels, tool permissions, data classes) and `evals` (test cases).
- **Branch protection:** nothing reaches `main` without a pull request.
- **Required approvers:** a `CODEOWNERS` file names HR policy owners for policy changes, HR and IT together for autonomy or tool changes, and the payroll lead for payroll rules.
- **Automatic checks:** Gitea Actions runs the eval suites on every pull request, and a change that fails can't merge.
- **Audit trail:** pull requests, reviews and merges are the change log auditors ask for.
- **Web editing:** HR staff can propose policy edits in the browser without using git commands.

The public GitHub repo holds the framework and templates. Each company's Gitea holds its own copy and configuration.

## Alternatives considered

- **Forgejo:** a community fork of Gitea under GPLv3, with the same workflow. A good choice if community governance matters more than an MIT license.
- **GitHub or GitLab cloud:** easy, but company policies and agent settings would live off-premises.
- **A shared drive or SharePoint:** familiar to HR, but no enforced reviews, no automatic tests, and a weaker audit trail.

## Consequences

- Needs an always-on machine: the desktop PC, a Mac, or a small server.
- HR staff need short training on pull requests in the web interface. A guide goes in `infra/gitea/`.
- Agents load their configuration only from approved commits on `main`.
