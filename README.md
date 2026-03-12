# claude-code-proxy

Route Claude Code through a GitHub Copilot or ChatGPT subscription via LiteLLM proxy.

With the Copilot config, Claude Code uses **real Claude models** (Opus 4.6, Sonnet 4.6, Haiku 4.5) through your Copilot subscription. With the ChatGPT config, requests go to GPT-5.4.

## Prerequisites

- [uv](https://docs.astral.sh/uv/getting-started/installation/) package manager
- A paid **GitHub Copilot** subscription and/or **ChatGPT Plus/Pro** subscription

## Setup

```bash
cd ~/dev/claude-code-proxy
uv sync
```

### Configure Claude Code (one-time)

If Claude Code is logged into a subscription, log out first:

```bash
claude /logout
```

Ensure `~/.claude.json` has `"hasCompletedOnboarding": true` to skip the interactive login flow. This is set automatically after your first normal Claude Code session.

The project-level `.claude/settings.json` handles the env vars (`ANTHROPIC_AUTH_TOKEN`, `ANTHROPIC_BASE_URL`, etc.) automatically when you run `claude` from this directory.

## Usage

Start the proxy (Terminal 1):

```bash
# GitHub Copilot (Claude Opus 4.6 / Sonnet 4.6 / Haiku 4.5)
uv run litellm --config litellm_config_copilot.yaml

# Or ChatGPT (GPT-5.4)
uv run litellm --config litellm_config_chatgpt.yaml
```

On first run, LiteLLM prints a device code + URL. Open the URL in your browser, enter the code, and authorize. Tokens are cached for subsequent runs.

The proxy starts on `http://localhost:4000`.

Launch Claude Code (Terminal 2):

```bash
cd ~/dev/claude-code-proxy
claude
```

It should show "API Usage Billing" (not your subscription) and connect to the proxy.

## Model mapping

### Copilot config (`litellm_config_copilot.yaml`)

| Claude Code requests                                        | Routed to (via Copilot) |
| ----------------------------------------------------------- | ----------------------- |
| `claude-opus-4-6` / `opus` / `claude-opus-4-20250514`       | `claude-opus-4.6`       |
| `claude-sonnet-4-6` / `sonnet` / `claude-sonnet-4-20250514` | `claude-sonnet-4.6`     |
| `claude-haiku-4-5-20251001` / `haiku`                       | `claude-haiku-4.5`      |

### ChatGPT config (`litellm_config_chatgpt.yaml`)

All models map to `gpt-5.4`.

## Limitations

- **Thinking/effort disabled**: Copilot's API does not support Anthropic's `thinking` parameter. `drop_params: true` silently drops it. Claude Code's effort setting (low/medium/high) has no effect. The model IS real Claude, just without extended thinking.
- **Agentic features may break**: Claude Code's tool use, file edits, and multi-step workflows rely on specific Anthropic response formats. Responses translated through LiteLLM may not match the expected contract perfectly.

## Reverting

To restore normal Claude Code operation, run `claude /logout` then log back in with your subscription.
