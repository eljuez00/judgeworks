# Governance

The rules every JudgeWorks agent runs under, and how they map to the standards. **Status: draft. Enforcement through [hooks](../hooks/) is still to be built.**

## Core controls

| Control | How JudgeWorks does it |
|---|---|
| Autonomy limits | Levels A0 to A3 per process. High-risk processes are capped at A1 ([process catalog](../processes/README.md)) |
| Data classification | Restricted, Confidential, Internal and Public. Restricted data goes to local models only ([design](../docs/design/overview.md#data-classes-and-model-routing)) |
| Least privilege | Each agent gets only its own tools. Read-only by default |
| Human oversight | A2 actions need a named approver. A1 output is a draft until a human sends it |
| Change control | Policies, prompts and agent settings change only through approved pull requests in local Gitea ([decision 0003](../docs/decisions/0003-gitea-change-control.md)) |
| Logging | Every model and tool call is recorded: who, what, when, inputs, outputs, decision |
| Testing | Pass/fail eval suites run on every change, and failures block the merge |
| Transparency | Employees are told when they're talking to an agent, and answers cite their sources |

## Standards mapping (summary)

| JudgeWorks control | NIST AI RMF 1.0 | ISO/IEC 42001 | EU AI Act |
|---|---|---|---|
| AI use policy, roles, approvers | Govern | AI policy, roles and responsibilities (Clause 5) | Deployer obligations (Art. 26), AI literacy (Art. 4) |
| Process catalog with risk tiers | Map | AI risk assessment and impact assessment (Clauses 6 and 8) | High-risk classification (Art. 6, Annex III point 4) |
| Data classes and synthetic data | Map, Manage | Data for AI systems (Annex A) | Data governance (Art. 10) |
| Eval suites | Measure | Performance evaluation (Clause 9) | Accuracy and robustness (Art. 15) |
| Autonomy caps, approvals | Manage | Operational controls (Clause 8) | Human oversight (Art. 14) |
| Audit log | Measure, Manage | Documented information, monitoring | Record-keeping (Art. 12) |
| Citations and agent disclosure | Govern, Manage | Information for interested parties (Annex A) | Transparency (Art. 13, Art. 50), informing workers (Art. 26) |

A detailed control-by-control mapping will live in `governance/mappings/`. This summary is a starting point, not legal advice.

## Planned documents

- `policy/ai-use-policy.md`: what agents may and may not do in HR
- `risk-register.md`: risks per process, with owners and mitigations
- `agent-cards/`: one card per agent covering purpose, data, autonomy, evals and known limits
- `mappings/`: full NIST, ISO and EU AI Act control mappings
