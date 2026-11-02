# 0001: Local LLM stack

- **Status:** Accepted
- **Date:** 2026-10-29

## Context

JudgeWorks needs a local AI environment for developing and testing HR agents. It must be able to:

1. write and fix code,
2. help develop Python scripts and programs,
3. call tools through MCP,
4. control a web browser.

It runs entirely on hardware we own. No prompts or code go to a cloud AI service.

Two machines are available:

| Machine | Role | Key specs |
|---|---|---|
| MacBook Air 15" | Daily driver, client | Apple M5, 16 GB unified memory, 512 GB SSD, macOS 26 |
| Desktop PC | Model server (later) | Ryzen 7 7800X3D, Radeon RX 7900 XT (20 GB), 32 GB DDR5, 2 TB NVMe, Windows 11 Pro |

## Decision

| Piece | Choice | Why |
|---|---|---|
| Model runner | **Ollama** | Free and open source (MIT), installs easily on the Mac, uses Apple's MLX engine, and offers a standard OpenAI-compatible API on the local machine |
| Model (Mac) | **Qwen 3.5 9B** (`qwen3.5:9b`) | About 7 GB, supports tool calling, long context, and fits in 16 GB with room left over |
| Model (PC, later) | Qwen 3.5 27B or 35B | Fits in the 7900 XT's 20 GB; to be benchmarked |
| Coding agent | **OpenCode** | Free (MIT) and runs in the terminal. It connects to tools through MCP, which Aider can't do natively |
| Python tooling | **uv** | One fast tool for Python versions, virtual environments and packages |
| Package manager | **Homebrew** | Standard way to install and update command-line tools on macOS |
| Browser control | **Playwright MCP** with its own Chromium | Separate automation browser with no saved logins |
| PC access (later) | **Cloudflare Tunnel** to a private network, reached with the Cloudflare WARP client | Nothing is exposed publicly, and the router has no open ports |

## Alternatives considered

- **LM Studio:** a good app for browsing and testing models, but heavier, and its server isn't as well suited to running all the time. Kept as a fallback.
- **Our own runner on llama.cpp (PC) and MLX (Mac):** possible, and could become a JudgeWorks project later. Ollama is faster to get working now.
- **VS Code with an AI extension:** not chosen, because a terminal agent is preferred.
- **Aider:** a mature terminal agent, but it has no native MCP support.
- **Exposing Ollama with a public tunnel or open port:** rejected. Ollama has no authentication.

## Consequences

- The Mac works fully offline once models are downloaded.
- A 9B model is good at focused coding and Python tasks. It's weaker at long, many-step browser tasks, so those are kept short and supervised.
- When the PC comes online, OpenCode can point at the bigger model over the private tunnel, and the Mac keeps the 9B model as an offline fallback.
- The PC's GPU is shared with other GPU workloads, so they run one at a time.

## Security rules

- No API keys, tunnel tokens, passwords, usernames or device names appear in this repo or on the public web page.
- Ollama listens only on the machine it runs on (`127.0.0.1`).
- The automation browser never uses an everyday browser profile.
