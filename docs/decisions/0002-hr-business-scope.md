# 0002: Business scope, autonomy and risk tiers

- **Status:** Proposed
- **Date:** 2026-11-02

## Context

JudgeWorks started as a local LLM setup. It is now a business-oriented harness for HR and HRIS. HR is a sensitive domain:

- The EU AI Act lists AI used in employment as **high-risk**. That covers recruitment, decisions on promotion, termination and task allocation, and monitoring or evaluating workers. It requires risk management, data governance, logging, transparency and human oversight.
- HR data includes pay, health and investigation records that must never leave controlled systems.

## Decision

1. **Domain:** HR and HRIS, covering the full hire-to-retire lifecycle in the [process catalog](../../processes/README.md).
2. **First reference agents:** History Watch (HRIS data audit), HR Services (employee support), Payroll Check (pre-release checks), and Time and Leave (exceptions).
3. **Autonomy levels A0 to A3,** defined in the [design overview](../design/overview.md). **Processes tagged high-risk are capped at A1 (draft only).**
4. **Data classes** (Restricted, Confidential, Internal, Public) decide which models may be used. **Restricted data goes to local models only.**
5. **Synthetic data only.** A fictional company is generated for every demo and test.
6. **Platforms:** Ollama, Claude and other cloud APIs, MCP tools, and Microsoft Power Platform.
7. **Standards mapped:** NIST AI RMF 1.0 (with the Generative AI Profile), ISO/IEC 42001, and the EU AI Act.
8. **Form:** a framework (governance, controls, evals) plus working reference agents.

## Alternatives considered

- **General enterprise scope:** broader, but less distinctive, and it dilutes the HRIS expertise behind the project.
- **Documentation only:** easier, but nothing proves the controls work.
- **Real or sample employer data:** rejected for privacy and confidentiality reasons.

## Consequences

- Every agent needs a risk tier, an autonomy level, a data class and an eval suite before it merges.
- High-risk processes such as hiring and termination are documented in the catalog, but their agents stay assistive.
- The model gateway and hooks become core components, because they're what enforce the rules.
