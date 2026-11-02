# JudgeWorks design overview

- **Status:** Draft for review
- **Date:** 2026-11-02

## What JudgeWorks is

A governed harness for AI agents in **HR and HRIS**. It includes:

- the rules agents run under,
- the tools they're allowed to use,
- the tests that prove they behave,
- the change control that lets an HR department own all of it,
- working reference agents for real HR processes, running on a **synthetic company** so everything can be public.

It is not an HR system. It sits beside one, such as PeopleSoft, Workday or ADP, and helps people use it safely.

## Design principles

1. **Decisions about people stay with people.** Agents can inform, draft and check. Where EU AI Act high-risk areas are concerned, such as hiring, pay, performance, discipline and termination, a human makes the call.
2. **Data classification decides the model.** Restricted HR data never leaves hardware the company controls.
3. **Least privilege.** Each agent gets only the tools and data its process needs.
4. **Everything is logged.** Every model call and tool call is recorded with who, what, when and why.
5. **Proven before trusted.** Each agent ships with pass/fail evals, and changes don't merge unless they pass.
6. **HR owns it.** In a company deployment, policies, prompts and agent settings live in a local Gitea that HR can review and approve. See [decision 0003](../decisions/0003-gitea-change-control.md).

## Architecture

```
                 ┌──────────────────────────────────────────────┐
 HR department → │  Gitea (local): policies, prompts, agent      │
                 │  configs, evals. Pull requests + approvals    │
                 └──────────────┬───────────────────────────────┘
                                │ approved config
 Employee / HR user             ▼
   │            ┌──────────────────────────────┐
   └──────────▶ │  Reference agents             │
                │  History Watch · HR Services  │
                │  Payroll Check · Time & Leave │
                └──────┬───────────────┬────────┘
                       │               │
             pre/post hooks      model gateway
         (policy, PII redaction,  (routes by data class:
          approvals, audit log)    Restricted → local only)
                       │               │
                       ▼               ▼
                ┌────────────┐   ┌─────────────────────────┐
                │ MCP tools  │   │ Ollama (local)           │
                │ HRIS, pay, │   │ Claude / cloud (approved │
                │ time,      │   │  data classes only)      │
                │ policies   │   │ Power Platform           │
                └─────┬──────┘   └─────────────────────────┘
                      ▼
          Synthetic data: ICI and its IHRS (generated, public)
```

### Layers

| Layer | Folder | Purpose |
|---|---|---|
| Governance | `governance/` | AI use policy, autonomy levels, risk register, control mappings to NIST AI RMF, ISO/IEC 42001 and the EU AI Act |
| Process catalog | `processes/` | Every HR process with its risk tier, allowed autonomy, data class and agent ideas |
| Reference agents | `agents/` | Working agents for the four first processes |
| Tools | `mcp/` | MCP servers exposing the synthetic HRIS, payroll, time and policy library, read-only by default |
| Hooks | `hooks/` | Code that runs before and after every action: policy checks, PII redaction, approvals, audit log |
| Model gateway | `platforms/` | Routing rules plus setup for Ollama, Claude and other APIs, and Power Platform (Copilot Studio, Power Automate) |
| Evals | `evals/` | Pass/fail test suites per agent, with pluggable judges (code checks, LLM judge, Jev-style decision model) |
| Data | `data/synthetic/` | Generator for a fictional company: employees, jobs, pay, time, leave and policies |
| Change control | `infra/gitea/` | Local Gitea setup, branch protection, HR approvers, automatic eval runs |

## Autonomy levels

| Level | Name | The agent may | Example |
|---|---|---|---|
| A0 | Inform | Read and report | "These 12 job records changed yesterday" |
| A1 | Draft | Prepare something for a human to review and send | A draft reply to an employee's leave question |
| A2 | Act with approval | Take an action after a named person approves | Open a payroll correction ticket once the payroll lead approves |
| A3 | Act and report | Act within narrow limits, then report | Send a missing-punch reminder to an employee |

**High-risk processes are capped at A1.** That covers hiring, compensation decisions, performance, employee relations and termination. The cap is enforced by hooks, not just written down.

## Data classes and model routing

| Class | Examples | Allowed models |
|---|---|---|
| **Restricted** | National IDs, pay, bank details, health and leave reasons, investigations | Local models only |
| **Confidential** | Names, job data, org structure, performance | Local, or approved cloud models under a data processing agreement, if the company allows it |
| **Internal** | Policies, procedures, anonymized reports | Local or approved cloud |
| **Public** | Published job posts, the HR Platform Library | Any |

The gateway classifies each request and refuses to send it to a model its class doesn't allow. The hooks redact what they can before any cloud call.

## The first four reference agents

| Agent | Process | Autonomy | What it proves |
|---|---|---|---|
| **History Watch** | HRIS data audit | A0 | It fingerprints job data daily, finds inserts, deletes and changes, including Correct History edits, and explains them in plain language |
| **HR Services** | Employee support | A1 | It answers questions from policies with citations, refuses out-of-scope questions and hands sensitive topics to a human |
| **Payroll Check** | Payroll pre-release | A0 to A2 | It compares a pay run with the last one, flags anomalies with reasons, and opens a correction ticket after approval |
| **Time and Leave** | Time and attendance, leave | A1 to A3 | It finds missing punches, overtime risks and leave-balance problems, then drafts or sends reminders within set limits |

## Platforms

| Platform | Role in JudgeWorks |
|---|---|
| **Ollama (local)** | Default for all Restricted data. Development on the Mac, production-grade on the desktop GPU |
| **Claude and other APIs** | Harder reasoning on Internal and approved Confidential data, through the same hooks and logs |
| **MCP** | The single way agents reach systems. Every tool is listed, scoped and logged |
| **Power Platform** | Patterns for enterprises already on Microsoft: Copilot Studio agents and Power Automate flows calling the same governed tools, with guides showing how the controls carry over |

## Not in scope

- Real employee or employer data. Everything is synthetic.
- Making hiring, pay, performance or termination decisions.
- Replacing an HRIS.

## Settled choices (2026-11-02)

| Question | Decision |
|---|---|
| Synthetic company | **International Corp Inc. (ICI)**, a fictional company. See [data/synthetic](../../data/synthetic/README.md) |
| HR system | **IHRS (ICI HR System)**, a fictional home-built HRIS with effective-dated records. It doesn't imitate any vendor product |
| Gitea | The project lives on GitHub. The repo includes **instructions** for a company to install and run its own local Gitea ([infra/gitea](../../infra/gitea/README.md)) |
| Languages | **Python** (agents, tools, data generator, evals), **TypeScript** (MCP servers and Power Platform custom connectors), and **Power Fx** where Copilot Studio and Power Automate need it |
