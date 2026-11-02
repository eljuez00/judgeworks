# Local LLM on a MacBook (Apple silicon)

A step-by-step setup for a fully local AI coding assistant on a Mac. Tested on a MacBook Air 15" with an Apple M5, 16 GB of memory, and macOS 26.6. This is the local development environment for the JudgeWorks harness.

**What you end up with:** Ollama running Qwen 3.5 9B, and OpenCode as a coding agent in Terminal. Nothing is sent to a cloud AI service.

Why these tools: see [decision 0001](../decisions/0001-local-llm-stack.md).

## Before you start

- About 30 to 50 GB of free disk space.
- An admin account on the Mac. Homebrew asks for your login password once.
- Terminal: press Cmd+Space, type "Terminal", and press Return.

Check what's already installed. This only reads information:

```sh
sw_vers -productVersion; sysctl -n machdep.cpu.brand_string; df -h / | tail -1
for t in brew python3 uv ollama git; do printf "%s: " $t; command -v $t || echo missing; done
```

## Step 1: Homebrew

Homebrew is the standard installer for developer tools on macOS.

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

- Press Return when it asks to continue, and enter your Mac password. Nothing appears on screen as you type.
- If Apple's Command Line Tools aren't installed yet, it installs them too.

Add Homebrew to your shell so `brew` works in every new Terminal window. These commands are for zsh, the default on macOS:

```sh
echo >> ~/.zprofile
echo 'eval "$(/opt/homebrew/bin/brew shellenv zsh)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv zsh)"
```

Optional: turn off Homebrew's anonymous usage statistics.

```sh
brew analytics off
```

## Step 2: Ollama, uv and OpenCode

```sh
brew install --cask ollama-app && brew install uv opencode
open -a Ollama
```

- **Ollama** runs models. After `open -a Ollama`, a llama icon appears in the menu bar and it keeps running in the background.
- **uv** manages Python versions, virtual environments and packages.
- **OpenCode** is the coding agent that runs in Terminal. Homebrew also installs `ripgrep`, which it uses to search code.

Check the versions:

```sh
ollama --version; uv --version; opencode --version
```

Versions used for this guide: Ollama 0.40.13, uv 0.13.6, OpenCode 2.2.1.

## Step 3: Download Qwen 3.5 9B and test it

Give Ollama a bigger context window, which is how much text the model keeps in view at once. A coding agent needs at least 16K to 32K tokens.

```sh
launchctl setenv OLLAMA_CONTEXT_LENGTH 32768
```

Then restart Ollama: click the llama icon in the menu bar, choose **Quit Ollama**, and run `open -a Ollama`.

**Simplest way:** llama icon → **Settings…** → set **Context length** to **32k**. Check it took effect by loading the model and running `ollama ps`. The CONTEXT column should read `32768`.

```
NAME          SIZE      PROCESSOR    CONTEXT    RUNNER
qwen3.5:9b    8.1 GB    100% GPU     32768      mlx
```

If CONTEXT still reads `4096`, the model was loaded before the change. A fallback that always works is to bake the setting into your own model variant, which reuses the same download:

```sh
printf 'FROM qwen3.5:9b\nPARAMETER num_ctx 32768\n' > ~/Qwen-32k.Modelfile
ollama create qwen3.5-9b-32k -f ~/Qwen-32k.Modelfile
```

On the M5, the model uses 7.7 GB of memory with a 4K context and 8.1 GB with 32K, all on the GPU, using Ollama's MLX engine.

> **Tips**
> - `osascript -e 'quit app "Ollama"'` can fail with "User canceled (-128)", because Ollama asks for confirmation. Quit from the menu bar instead.
> - Running `ollama` with nothing after it only prints the help text. It isn't an error.
> - The "Run Ollama" welcome window that appears on first launch can be closed.

Check that it's running, then download the model (about 7 GB):

```sh
curl -s localhost:11434          # prints "Ollama is running"
ollama pull qwen3.5:9b
```

First test, with timing:

```sh
ollama run qwen3.5:9b --verbose "Write a short Python function that converts a MIDI note number to its note name, like 60 -> C4."
```

The model "thinks" first, then answers. It produced a correct function: 60 gives C4, 59 gives B3, 72 gives C5. We checked it by running the code.

### Results on a MacBook Air M5, 16 GB

| Measure | Result |
|---|---|
| First load into memory | 4.5 s |
| Reading the prompt | 15 tokens/s (a 34-token prompt) |
| **Writing the answer** | **32.8 tokens/s** |
| Total time for this answer | 17.8 s |

About 33 tokens per second is roughly 25 words per second, which is faster than you can read.

## Step 4: Connect OpenCode to the local model

Copy the settings file from this repo: [`config/opencode/opencode.json`](../../config/opencode/opencode.json).

```sh
mkdir -p ~/.config/opencode
cp config/opencode/opencode.json ~/.config/opencode/opencode.json
python3 -m json.tool ~/.config/opencode/opencode.json > /dev/null && echo "CONFIG OK"
```

What it sets:

| Setting | Why |
|---|---|
| `provider.ollama` with `baseURL` `http://localhost:11434/v1` | Talk to Ollama on this Mac |
| `model` and `small_model` set to `ollama/qwen3.5:9b` | All requests stay local, including background ones like naming sessions |
| `disabled_providers: ["opencode"]` | Blocks OpenCode's built-in free **cloud** models, so it can't quietly fall back to them |
| `permission` | Edits and routine commands run without asking. Deleting, downloads, `sudo`, `git push` and `brew` ask first |

> **Check the model in the bottom bar.** It must say `Qwen 3.5 9B (local) · Ollama (local)`. If it shows a cloud model, such as "Flint 40B Free · OpenCode", the config didn't load. Stop and fix it before typing anything.

Work in a sandbox folder. OpenCode works on the folder you start it in.

```sh
mkdir -p ~/judgeworks-sandbox && cd ~/judgeworks-sandbox && opencode
```

> **Tip:** you can run several Terminal windows (Cmd+N) side by side: OpenCode in one, and normal commands like `ollama ps` in the other.

### Tool-use test

> Create a file midi_names.py with a function that converts MIDI note numbers to note names, then run it with python3 to print the names for 60, 64 and 67.

**What happened on the M5:**

1. **First try:** it created the file and ran it, so tool use works. But it left out the octave (`C` instead of `C4`), and it confidently explained the results wrongly. It called MIDI 6 a "high F sharp" when it's one of the lowest notes.
2. **Fix request:**
   > The output is missing the octave. Fix midi_names.py so 60 prints C4 (MIDI 60 is middle C, octave = note // 12 - 1), and print 60, 64 and 67.

   It edited the file and showed the change as a diff. It tried `python`, which isn't on macOS, then recovered by itself with `python3`, and printed `C4, E4, G4`. Correct.

**Speed inside OpenCode:** the first task took 2 min 18 s at an average of 2.7 tokens/s. OpenCode sends about 6,000 tokens of instructions with every request, and reading those takes the Mac far longer than writing the answer. The second task showed 25 minutes, but that included time waiting for approval clicks.

### Lessons

- **Check everything.** A 9B model can be wrong and sound sure of itself. Run the code, and don't trust its explanations.
- **Give exact specs.** It got the octave right once the rule was written into the prompt.
- **Approval rules are a balance.** Asking every time is safe but slow, and allowing everything is risky. The config allows routine actions and asks for risky ones. Only start OpenCode inside a sandbox folder kept in git, so `git restore .` undoes mistakes.
- **Keep requests short and focused.** Big multi-step jobs are slow on 16 GB, and the desktop GPU is for those.

## Step 5: Python projects with uv

Keep each project separate from the Mac's built-in Python:

```sh
cd ~/judgeworks-sandbox
uv init my-project && cd my-project
uv add pandas
uv run python main.py
```

- `uv init` creates a project folder. It also creates a small git repo inside it. If the parent folder is already a git repo, delete the inner one with `rm -rf my-project/.git`, because git won't add one repo inside another.
- `uv add` installs packages into the project's own environment.
- `uv run` runs code in that environment. No manual "activate" step is needed.

Set your git name and email once. GitHub's private "noreply" address keeps your real email out of commits:

```sh
git config --global user.name "your-github-username"
git config --global user.email "ID+username@users.noreply.github.com"
```

## Lessons from tool-heavy agent sessions

We connected a third-party MCP tool server, a connector that gives the agent a set of named tools, and measured what happened on the M5:

| Measure | Result |
|---|---|
| Prompt size per request | about 14,500 tokens (agent instructions plus tool descriptions) |
| Prompt reading speed | about 600 tokens/s, so about 25 s before each reply starts |
| Failure seen | `qwen3.5 tool call parsing failed: XML syntax error` caused an HTTP 500, and OpenCode waited forever |
| Peak memory | 10.1 GiB with another heavy app open |

**What fixed it:**

- **A dedicated agent with only the tools it needs.** OpenCode agents can turn off unused tools, which shrinks the prompt and leaves fewer ways to go wrong.
- **Low temperature (0.1)** for agents that mostly call tools. Their output is more predictable, so tool calls are formatted correctly more often.
- **One request at a time,** and a fresh session (`/new`) when the model starts looping.
- **Trust tool data, not model arithmetic.** In one test the model correctly reported a value from a tool, then got its own calculation from that value wrong by a factor of two.

**Rule of thumb for a 9B model:** it should make the decisions, while tested tools do the precise work. Asking it to write precise, specialized code from scratch, such as binary file formats, produced files that looked valid but were wrong.

## Heat and memory on a fanless MacBook Air

The Air has no fan. Under sustained load it slows itself down to stay cool. That's safe, but you'll see fewer tokens per second.

```sh
pmset -g therm                                   # any thermal warnings?
sudo powermetrics --samplers thermal -n 1        # Nominal / Moderate / Heavy
memory_pressure | tail -1; sysctl vm.swapusage   # free memory and swap
```

- **Memory matters more than heat on 16 GB.** We saw 14% free and 5.8 GB of swap before a restart. Watch the Memory Pressure graph in Activity Monitor; yellow or red means close something.
- Restart weekly. Unload the model when you're done (`ollama stop qwen3.5:9b`). Don't run other memory-heavy apps alongside the model.
- Use it on a hard surface, and leave Optimized Battery Charging on.

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `ollama` prints a list of commands | Run with nothing after it | Add a command, e.g. `ollama ps` |
| `quit app "Ollama"` gives "User canceled (-128)" | Ollama asks for confirmation | Quit from the menu bar icon |
| `ollama ps` shows CONTEXT 4096 | Setting not applied | Ollama Settings → Context length 32k, then reload the model, or use [`config/ollama/Qwen-32k.Modelfile`](../../config/ollama/Qwen-32k.Modelfile) |
| `zsh: command not found` after pasting | Output text was pasted along with the command | Paste only the command lines |
| `ollama run model "prompt"` and then `/bye` fails | With a prompt on the command line it answers and exits by itself | `/bye` is only for the interactive `>>>` prompt |
| OpenCode shows a cloud model | The config didn't load | Check it with `python3 -m json.tool`, then use `/models` |
| OpenCode waits forever | The model sent a broken tool call (HTTP 500) | Esc, `/new`, then check `~/.ollama/logs/server.log` |
| Model loops on the same mistake | Small-model failure mode | Esc, `/new`, and a smaller, more exact request |
| `git add` fails: "does not have a commit checked out" | A git repo inside another | `rm -rf <subfolder>/.git` |

The Ollama log is the best diagnostic. It shows prompt size, reading speed and tool-call errors:

```sh
tail -f ~/.ollama/logs/server.log | grep -iE "progress|tool call|500"
```
