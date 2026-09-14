# Codex CLI Tips

Collection of tips and tricks for [OpenAI's Codex CLI](https://developers.openai.com/codex/), the terminal coding agent.

## Table of Contents

### General
- [Tip 1: Resume, fork, and archive sessions instead of starting over](#tip-1-resume-fork-and-archive-sessions-instead-of-starting-over)
- [Tip 2: Switch models and reasoning effort without restarting](#tip-2-switch-models-and-reasoning-effort-without-restarting)
- [Tip 3: Learn the slash commands worth knowing](#tip-3-learn-the-slash-commands-worth-knowing)
- [Tip 4: Speed up your keyboard flow in the TUI](#tip-4-speed-up-your-keyboard-flow-in-the-tui)
- [Tip 5: Attach a screenshot instead of describing it](#tip-5-attach-a-screenshot-instead-of-describing-it)
- [Tip 6: Pick the right login for the context](#tip-6-pick-the-right-login-for-the-context)

### Command
- [Tip 1: Script Codex with codex exec instead of the interactive TUI](#tip-1-script-codex-with-codex-exec-instead-of-the-interactive-tui)
- [Tip 2: Sandbox level and approval policy are two separate dials](#tip-2-sandbox-level-and-approval-policy-are-two-separate-dials)
- [Tip 3: Get a second opinion with codex review before committing](#tip-3-get-a-second-opinion-with-codex-review-before-committing)
- [Tip 4: Test what a command is allowed to do with codex sandbox](#tip-4-test-what-a-command-is-allowed-to-do-with-codex-sandbox)
- [Tip 5: Force machine-readable output with --json and --output-schema](#tip-5-force-machine-readable-output-with---json-and---output-schema)
- [Tip 6: Keep multiple setups ready with config profiles](#tip-6-keep-multiple-setups-ready-with-config-profiles)

### Agent
- [Tip 1: Delegate noisy work to subagents so the main thread stays clean](#tip-1-delegate-noisy-work-to-subagents-so-the-main-thread-stays-clean)
- [Tip 2: Give a subagent a fixed identity with a TOML file](#tip-2-give-a-subagent-a-fixed-identity-with-a-toml-file)
- [Tip 3: Cap concurrency and set subagent defaults globally](#tip-3-cap-concurrency-and-set-subagent-defaults-globally)

### Skill
- [Tip 1: Custom prompts are deprecated in favor of skills](#tip-1-custom-prompts-are-deprecated-in-favor-of-skills)
- [Tip 2: Skills are picked automatically, or called by name](#tip-2-skills-are-picked-automatically-or-called-by-name)
- [Tip 3: A skill can grow into a plugin without a rewrite](#tip-3-a-skill-can-grow-into-a-plugin-without-a-rewrite)
- [Tip 4: Install plugins from the shared ChatGPT and Codex marketplace](#tip-4-install-plugins-from-the-shared-chatgpt-and-codex-marketplace)

### Mcp
- [Tip 1: Add an MCP server in one command](#tip-1-add-an-mcp-server-in-one-command)
- [Tip 2: Codex is an MCP client only, not a server](#tip-2-codex-is-an-mcp-client-only-not-a-server)
- [Tip 3: Approve MCP tools per server and per tool](#tip-3-approve-mcp-tools-per-server-and-per-tool)
- [Tip 4: Log into OAuth-protected MCP servers](#tip-4-log-into-oauth-protected-mcp-servers)

### Prompt
- [Tip 1: AGENTS.md is layered, and the closest file wins](#tip-1-agentsmd-is-layered-and-the-closest-file-wins)
- [Tip 2: Give PR reviews their own rules in AGENTS.md](#tip-2-give-pr-reviews-their-own-rules-in-agentsmd)
- [Tip 3: Keep AGENTS.md lean](#tip-3-keep-agentsmd-lean)

### Hooks
- [Tip 1: notify is Codex's one lifecycle hook](#tip-1-notify-is-codexs-one-lifecycle-hook)
- [Tip 2: Turn a turn-complete hook into a notification](#tip-2-turn-a-turn-complete-hook-into-a-notification)

### Workflow
- [Tip 1: Let @codex review pull requests before a human does](#tip-1-let-codex-review-pull-requests-before-a-human-does)
- [Tip 2: Run Codex in CI with the official GitHub Action](#tip-2-run-codex-in-ci-with-the-official-github-action)

## General

### Tip 1: Resume, fork, and archive sessions instead of starting over

Codex CLI writes every session to a rollout file under `~/.codex/sessions/`, so nothing is lost when you close the terminal. `codex resume` reopens your most recent session in the current directory, or pass `--all` to search across every directory you've worked in. `codex fork` starts a new session from an existing transcript while leaving the original untouched, which is handy when you want to try a different direction without losing the first attempt. Sessions you're done with can be archived or deleted outright, keeping the resume list to work you'd actually return to. Treat sessions the way you'd treat git branches: one per task, named in your head by what it was for, not one long-running thread for everything.

Example:
```
codex resume --last
codex fork --last
```

Reference: [Codex CLI reference](https://developers.openai.com/codex/cli/reference)

### Tip 2: Switch models and reasoning effort without restarting

You don't need to relaunch Codex to change how it thinks. Run `/model` inside a session to swap models or reasoning effort on the fly, or set `--model`/`-m` on the command line to start with a specific one. Reasoning effort is a separate knob — `minimal`, `low`, `medium`, `high`, or `xhigh` — configurable per session or as `model_reasoning_effort` in `config.toml`. Save the higher effort levels for genuinely hard problems; they cost more time and tokens for a turn that a lower effort level would often solve just as well.

Example:
```
codex --model gpt-5.1-codex
```
Then mid-session:
```
/model
```

Reference: [Config reference](https://developers.openai.com/codex/config-reference)

### Tip 3: Learn the slash commands worth knowing

A handful of built-in slash commands cover most of what you'd otherwise dig through menus for. `/status` shows your current session state, `/diff` shows pending changes, `/mcp` lists connected MCP servers and their tools, `/approvals` lets you adjust the approval policy mid-session, `/review` kicks off a code review, and `/ide` pulls in context from your editor. Type `/` on its own to see the full list available in your version. These are worth learning early since they replace a lot of "how do I check X" round-trips with the agent itself.

Reference: [Codex CLI reference](https://developers.openai.com/codex/cli/reference)

### Tip 4: Speed up your keyboard flow in the TUI

The composer has a few shortcuts that save real time once they're muscle memory. Type `@` to search and insert a file path without leaving the keyboard. `Ctrl+R` searches your prompt history the way it does in a shell. A `!` prefix runs a one-off local shell command inline without starting a full turn. Press `Tab` while a turn is running to queue a follow-up prompt instead of waiting idle, and `Esc` `Esc` on an empty composer edits your previous message and forks the conversation from there.

Reference: [Codex CLI reference](https://developers.openai.com/codex/cli/reference)

### Tip 5: Attach a screenshot instead of describing it

Codex CLI accepts images directly, so an error screenshot, a design mock, or an architecture diagram doesn't need to be transcribed into text first. Pass `--image`/`-i` with a path when you launch a prompt, or paste an image straight into the composer during an interactive session. This is most useful for the things that are painful to describe accurately in words: a stack trace rendered in a terminal screenshot, a Figma frame, or a whiteboard photo of a system diagram.

Example:
```
codex -i ./screenshots/error.png "why is this failing?"
```

Reference: [Image inputs](https://developers.openai.com/codex/image-inputs)

### Tip 6: Pick the right login for the context

`codex login` supports a ChatGPT account (browser OAuth), a device-code flow for headless machines (`--device-auth`), or a plain API key (`--with-api-key`). Interactive local use is usually simplest with your ChatGPT login, since it's tied to your existing plan. Scripts and CI should use `CODEX_API_KEY` instead — it's metered independently and doesn't depend on a browser being available. Run `codex login status` to check which one is active before you're deep into a task and hit an auth error.

Example:
```
CODEX_API_KEY=sk-... codex exec "run the test suite and summarize failures"
```

Reference: [Codex CLI reference](https://developers.openai.com/codex/cli/reference)

## Command

### Tip 1: Script Codex with codex exec instead of the interactive TUI

`codex exec` (alias `codex e`) runs a single task headlessly: no TUI, progress goes to stderr, and only the final agent message is printed to stdout, so it pipes cleanly into other tools. It reads a prompt from stdin with `codex exec -`, which makes it easy to feed it the output of another command. `codex exec resume [--last]` continues a prior non-interactive run the same way `codex resume` does for interactive ones. This is the mode to reach for any time Codex is one step in a larger script rather than the whole interaction.

Example:
```
npm test 2>&1 | codex exec "summarize the failures and suggest a fix"
```

Reference: [Non-interactive mode](https://developers.openai.com/codex/noninteractive)

### Tip 2: Sandbox level and approval policy are two separate dials

Older Codex versions had combined modes like `suggest` or `full-auto`. Current versions split this into two independent settings: `--sandbox` (`read-only`, `workspace-write`, or `danger-full-access`) controls what the agent can touch, and `--ask-for-approval` (`on-request` or `never`) controls whether it pauses to ask first. `workspace-write` confines file writes to your project directory and denies network access by default, even for commands that would otherwise be allowed to run silently. Thinking of these as two dials instead of one preset makes it much easier to reason about what a given combination actually permits.

Example:
```
codex --sandbox workspace-write --ask-for-approval on-request
```

Reference: [Sandboxing](https://developers.openai.com/codex/sandboxing)

### Tip 3: Get a second opinion with codex review before committing

`codex review` runs a non-interactive review pass without touching your working tree, and prints prioritized findings instead of a wall of nitpicks. Point it at your uncommitted changes, a specific commit, or a diff against a base branch. It's a good habit to run before opening a PR, especially on a change you wrote quickly and want a second pass on before a human reviewer sees it.

Example:
```
codex review --uncommitted
codex review --base main
```

Reference: [Codex CLI reference](https://developers.openai.com/codex/cli/reference)

### Tip 4: Test what a command is allowed to do with codex sandbox

`codex sandbox` runs an arbitrary command directly under Codex's sandbox policy, without invoking the agent at all. It's a fast way to check whether a specific command would be blocked, need approval, or run silently under your current sandbox and approval settings, instead of finding out mid-task when the agent hits it. Useful when you're tuning a sandbox config for a new project and want to verify the boundaries before trusting the agent to run inside them.

Reference: [Sandboxing](https://developers.openai.com/codex/sandboxing)

### Tip 5: Force machine-readable output with --json and --output-schema

`codex exec --json` streams structured JSON Lines events (`thread.started`, `turn.completed`, `item.completed`, and so on) instead of plain text, which is what you want when another program is consuming Codex's output rather than a person. `--output-schema <path>` goes further and constrains the final response itself to a JSON Schema you provide, so downstream code can parse it without guessing at format. Combine both when you're wiring Codex into a pipeline that needs a reliable, typed result rather than prose to scrape.

Example:
```
codex exec --json --output-schema ./schema.json "classify this ticket" > result.jsonl
```

Reference: [Non-interactive mode](https://developers.openai.com/codex/noninteractive)

### Tip 6: Keep multiple setups ready with config profiles

Instead of editing `config.toml` every time you switch contexts, define separate profile files under `$CODEX_HOME` (for example `~/.codex/work.config.toml`) and select one with `--profile`/`-p`. A profile can carry its own model, sandbox defaults, or MCP servers, so switching between a locked-down client project and an experimental personal one is a single flag instead of a manual edit.

Example:
```
codex --profile work
```

Reference: [Config reference](https://developers.openai.com/codex/config-reference)

## Agent

### Tip 1: Delegate noisy work to subagents so the main thread stays clean

Subagents let Codex spawn separate threads for work whose intermediate output you don't want cluttering your main conversation — exploring a large codebase, running a test suite, or fanning out across independent files. Ask directly for delegation, or use `/agent` to manage threads yourself. Codex waits for all subagents to finish and folds their results back into a single consolidated response, so you get the benefit of parallel work without having to babysit multiple windows.

Example:
```
Spawn two agents: one to find every place we call the old API, one to draft the migration for each.
```

Reference: [Subagents](https://developers.openai.com/codex/agent-configuration/subagents)

### Tip 2: Give a subagent a fixed identity with a TOML file

Rather than re-describing a role every time, define it once as a TOML file under `~/.codex/agents/` (personal) or `.codex/agents/` (project). At minimum it needs a `name`, `description`, and `developer_instructions`; it can also pin a specific `model`, reasoning effort, or `sandbox_mode` so a review-focused subagent, say, always runs read-only regardless of your main session's settings.

Example:
```toml
name = "pr_reviewer"
description = "Reviews diffs for correctness and test coverage"
sandbox_mode = "read-only"
developer_instructions = """
Review like an owner: correctness, security, and missing tests first.
"""
```

Reference: [Subagents](https://developers.openai.com/codex/agent-configuration/subagents)

### Tip 3: Cap concurrency and set subagent defaults globally

The `[agents]` block in `config.toml` sets defaults so you don't have to repeat them per subagent definition: `default_subagent_model`, `default_subagent_reasoning_effort`, and `max_concurrent_threads_per_session` to keep a large fan-out from spawning more parallel threads than you actually want running at once.

Example:
```toml
[agents]
max_concurrent_threads_per_session = 4
default_subagent_reasoning_effort = "medium"
```

Reference: [Subagents](https://developers.openai.com/codex/agent-configuration/subagents)

## Skill

### Tip 1: Custom prompts are deprecated in favor of skills

Codex used to support reusable prompt templates as markdown files under `~/.codex/prompts/`, invoked as `/prompts:<name>`. That mechanism is now deprecated — the docs point to skills as the replacement. If you have old custom prompts lying around, treat them as a todo list for skills to write, not something to keep building on.

Reference: [Custom prompts](https://learn.chatgpt.com/docs/custom-prompts)

### Tip 2: Skills are picked automatically, or called by name

A skill is a directory with a `SKILL.md` file describing what it does and when to use it. Codex loads only the name and description for every skill up front, and pulls in the full instructions only once a task actually matches — so you can have many skills installed without bloating every prompt. You can also invoke one explicitly by typing `$` followed by its name, or browse what's available with `/skills`, instead of waiting for automatic matching.

Example:
```
$skill-creator
```

Reference: [Build skills](https://learn.chatgpt.com/docs/build-skills)

### Tip 3: A skill can grow into a plugin without a rewrite

A skill on its own is just `SKILL.md` plus optional `scripts/`, `references/`, and `assets/` folders. A plugin wraps that same shape with more: an MCP server, lifecycle hooks, and UI metadata, so a skill you wrote for yourself can grow into something distributable without restructuring what you already built. The documented advice is to start with the smallest shape that solves your problem and add the rest only when you actually need wider distribution.

Reference: [Plugins](https://developers.openai.com/plugins/concepts/plugins)

### Tip 4: Install plugins from the shared ChatGPT and Codex marketplace

Plugins live in a marketplace shared between ChatGPT and Codex, so something built for one surface is installable from the other. Manage them with `codex plugin` (install, list, remove), or use a `codex://plugins/install/<name>?marketplace=<marketplace>` deep link shared by someone else. Check what's already published before writing a skill from scratch — there's a decent chance the workflow you want already exists.

Example:
```
codex plugin list
```

Reference: [Plugins](https://developers.openai.com/plugins/concepts/plugins)

## Mcp

### Tip 1: Add an MCP server in one command

`codex mcp add <name> -- <command>` registers a stdio MCP server without hand-editing `config.toml`, and `codex mcp list` shows everything currently configured. The same `[mcp_servers.*]` config is shared across Codex CLI, the ChatGPT desktop app, and the IDE extension, so a server you add once in the CLI shows up in all three.

Example:
```
codex mcp add context7 -- npx -y @upstash/context7-mcp
codex mcp list
```

Reference: [MCP](https://developers.openai.com/codex/mcp)

### Tip 2: Codex is an MCP client only, not a server

Codex CLI consumes MCP servers, but it can no longer be hosted as one itself — the old `codex mcp-server` command has been removed. If you need to expose Codex's capabilities to another MCP-speaking client, that path doesn't exist anymore; the experimental `codex app-server` is OpenAI's current answer for programmatic integration, but it speaks its own JSON-RPC protocol, not MCP, and isn't meant for production use yet.

Reference: [MCP](https://developers.openai.com/codex/mcp)

### Tip 3: Approve MCP tools per server and per tool

Approval for MCP tools isn't all-or-nothing. Each server entry in `config.toml` can restrict which tools are exposed at all (`enabled_tools`/`disabled_tools`), and set a default approval mode per tool under `[mcp_servers.<id>.tools.<tool>]`. This is worth doing for any server that has both safe read tools and destructive write tools — you can let the reads run silently while still requiring approval for anything that mutates state.

Example:
```toml
[mcp_servers.chrome_devtools]
url = "http://localhost:3000/mcp"
enabled_tools = ["open", "screenshot"]
default_tools_approval_mode = "prompt"
```

Reference: [Config reference](https://developers.openai.com/codex/config-reference)

### Tip 4: Log into OAuth-protected MCP servers

Some MCP servers authenticate via OAuth instead of a static token. `codex mcp login <server-name>` runs that login flow directly, separately from adding the server itself, so you don't need to hand-craft a bearer token or manage refresh manually.

Example:
```
codex mcp login figma
```

Reference: [MCP](https://developers.openai.com/codex/mcp)

## Prompt

### Tip 1: AGENTS.md is layered, and the closest file wins

Codex reads AGENTS.md from more than one place: a global file in `~/.codex/`, then every directory from your git root down to your current working directory. All of them get concatenated into the final prompt in that order, which means instructions in a file closer to your cwd effectively override the broader ones that came before them. An `AGENTS.override.md` in any of those directories takes priority over a plain `AGENTS.md` in the same directory. Use this to keep a broad, stable set of project rules at the root and narrower, situational rules in the subdirectories that actually need them.

Reference: [AGENTS.md guide](https://developers.openai.com/codex/guides/agents-md)

### Tip 2: Give PR reviews their own rules in AGENTS.md

When Codex reviews a pull request — either through `@codex review` or an automatic repo setting — it reads a `## Code Review Rules` section in AGENTS.md to know what to focus on. Use it for the things generic review misses: domain-specific invariants, a security concern unique to this codebase, or a pattern your team keeps having to point out in review. Skip rules for anything CI already checks mechanically; that's not what this section is for.

Reference: [AGENTS.md guide](https://developers.openai.com/codex/guides/agents-md)

### Tip 3: Keep AGENTS.md lean

Because every discovered AGENTS.md file gets concatenated into one combined prompt, a long file in a deeply nested directory adds weight to every single turn, not just the ones that need it. There's a default 32 KiB cap on the combined size (configurable via `project_doc_max_bytes`), and hitting it silently drops content rather than erroring loudly. Treat AGENTS.md the way you'd treat any other prompt you pay for on every turn: state the rule, skip the explanation of why it exists, and put anything that needs real elaboration in a linked doc instead.

Reference: [AGENTS.md guide](https://developers.openai.com/codex/guides/agents-md)

## Hooks

### Tip 1: notify is Codex's one lifecycle hook

Codex doesn't have a hook system as broad as pre- and post-tool-use hooks — there's a single `notify` config key, and it fires on exactly one event: an agent turn completing. Set it to a command in `config.toml`, and Codex invokes it with a JSON payload passed as a single command-line argument, not over stdin.

Example:
```toml
notify = ["python3", "/path/to/notify.py"]
```

Reference: [Config advanced](https://learn.chatgpt.com/docs/config-file/config-advanced)

### Tip 2: Turn a turn-complete hook into a notification

The JSON argument your `notify` script receives includes the event type, thread and turn IDs, the working directory, and the last assistant message — enough to build a real notification without querying Codex for more context. A short script that checks for `type == "agent-turn-complete"` and forwards the last message is enough to get a desktop toast or a Slack ping every time a long-running task finishes.

Example:
```python
import sys, json

payload = json.loads(sys.argv[1])
if payload.get("type") == "agent-turn-complete":
    print(f"Codex finished: {payload.get('last-assistant-message', '')[:200]}")
```

Reference: [Config advanced](https://learn.chatgpt.com/docs/config-file/config-advanced)

## Workflow

### Tip 1: Let @codex review pull requests before a human does

Mentioning `@codex review` on a GitHub PR gets an automated pass focused on P0/P1 issues, or a repo setting can turn this on for every PR without a mention. You can also direct it at something narrower — `@codex fix the CI failures`, or `@codex security review` — and it starts a cloud chat scoped to that PR. This doesn't replace human review, but it catches the kind of issue that's easy to miss in a large diff before a person spends time on it.

Reference: [GitHub integration](https://learn.chatgpt.com/docs/third-party/github)

### Tip 2: Run Codex in CI with the official GitHub Action

For CI runs you control yourself, `openai/codex-action` is the supported path instead of scripting `codex exec` around raw API calls. It defaults to a `drop-sudo` safety strategy that revokes sudo and blocks the Docker socket before Codex runs, and the documented pattern is to run Codex read-only in one job to produce a diff or PR comment, then hand off to a separate job with write permissions to actually open the PR — keeping your API key out of the privileged context entirely.

Example:
```yaml
on:
  pull_request:
    types: [opened, synchronize, reopened]
jobs:
  codex:
    steps:
      - uses: actions/checkout@v5
      - uses: openai/codex-action@v1
        with:
          openai-api-key: ${{ secrets.OPENAI_API_KEY }}
          prompt-file: .github/codex/prompts/review.md
          output-file: codex-output.md
```

Reference: [GitHub Action](https://developers.openai.com/codex/github-action)
