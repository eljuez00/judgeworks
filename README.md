# JudgeWorks

**A governed harness for AI agents in HR and HRIS.** It covers the rules agents run under, the tools they may use, the tests that prove they behave, and change control the HR department owns, with working reference agents running on a fictional company, **International Corp Inc. (ICI)**, and its home-built HR system, **IHRS**.

> **Status: design phase.** The local development environment works and is documented. The governance framework and first reference agents are being designed. See the [design overview](docs/design/overview.md).

## Why

AI used in employment decisions is high-risk under the EU AI Act, and HR data is some of the most sensitive data a company holds. JudgeWorks shows how to use agents in HR safely:

- **People decide about people.** Agents inform, draft and check; humans make hiring, pay, performance and termination calls.
- **Restricted data stays local.** Pay, health and investigation data is only ever processed by local models.
- **Every action is logged and every change is approved,** through a local Gitea that the HR department controls.
- **Every agent is tested** with pass/fail evals before it ships.

## First reference agents

| Agent | HR process | Autonomy |
|---|---|---|
| **History Watch** | HRIS data audit: detect and explain daily changes, including Correct History edits | Inform |
| **HR Services** | Employee questions answered from policy, with citations and a human handoff | Draft |
| **Payroll Check** | Pre-release pay-run comparison and anomaly flags | Inform, then act with approval |
| **Time and Leave** | Missing punches, overtime risk, leave-balance problems | Draft, plus limited reminders |

The [process catalog](processes/README.md) covers the full hire-to-retire lifecycle, with risk tiers and autonomy limits.

## Repository map

| Path | Contents |
|---|---|
| [`docs/design/`](docs/design/overview.md) | Architecture, autonomy levels, data classes, model routing |
| [`docs/decisions/`](docs/decisions/) | Decision records: local stack, HR scope, Gitea change control |
| [`processes/`](processes/README.md) | HR process catalog with risk tiers |
| [`governance/`](governance/README.md) | Controls mapped to NIST AI RMF, ISO/IEC 42001 and the EU AI Act |
| `agents/`, `mcp/`, `hooks/`, `evals/` | Reference agents, governed tools, enforcement hooks, test suites (planned) |
| `platforms/` | Ollama, Claude and other APIs, Microsoft Power Platform (planned) |
| [`data/synthetic/`](data/synthetic/README.md) | ICI and the IHRS schema; generator planned |
| [`infra/gitea/`](infra/gitea/README.md) | How a company installs its own local Gitea for HR change control |
| [`docs/guides/mac-local-llm.md`](docs/guides/mac-local-llm.md) | Local development environment: Ollama, Qwen 3.5 9B, OpenCode and uv on an M5 MacBook Air |
| [`docs/journal/`](docs/journal/) | Dated build notes, including mistakes and fixes |
| [`AGENTS.md`](AGENTS.md) | Rules for AI agents working in this repo |

## Roadmap

- [x] Local development environment (Mac)
- [x] Scope, process catalog and design draft
- [ ] Synthetic company and data generator
- [ ] Governance: AI use policy, risk register, agent cards, full control mappings
- [ ] Gateway and hooks: data-class routing, PII redaction, approvals, audit log
- [ ] MCP tools over the synthetic HRIS, payroll, time and policies
- [ ] Reference agents: History Watch, HR Services, Payroll Check, Time and Leave
- [ ] Eval suites running automatically in local Gitea
- [ ] Desktop GPU model server and Power Platform patterns

## Languages

Python for agents, tools, data and evals. TypeScript for MCP servers and Power Platform custom connectors. Power Fx where Copilot Studio and Power Automate need it.

## Data

All demo data is **synthetic**: generated people at International Corp Inc. (ICI), a fictional company. No real employee or employer data is used.

## Author

Built by [Jacob Frie](https://eljuezproductions.com/about), an HRIS analyst who builds AI agents for regulated enterprise systems.

## License

[MIT](LICENSE). Nothing here is legal advice. Check compliance obligations with qualified counsel.
