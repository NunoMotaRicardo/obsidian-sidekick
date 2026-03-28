# How to configure the Telegram bot in Sidekick

This guide explains how to connect your Telegram bot to Sidekick inside Obsidian and tune its behavior.

> **Prerequisite:** You need a bot token and your Telegram user ID. See the [Telegram bot creation guide](telegram-bot-creation-guide.md) if you haven't set those up yet.

---

## Step 1: Open the Sidekick settings

1. In Obsidian, go to **Settings → Community plugins → Sidekick** (gear icon).
2. Select the **Bots** tab in the Sidekick settings panel.

You'll see the **Telegram** section.

---

## Step 2: Enter your bot credentials

| Setting | What to enter |
|---|---|
| **Bot identifier** | The bot's numeric ID or username (informational — helps you identify which bot is configured). |
| **Bot token** | The full token from BotFather (e.g. `123456789:ABCdefGHI-jklMNOpqrSTUvwxYZ`). This is stored securely in Obsidian's vault-local storage, not in `data.json`. |

---

## Step 3: Configure allowed users

In the **Allowed users** field, enter a comma-separated list of numeric Telegram user IDs that are permitted to interact with the bot.

```
987654321, 123456780
```

This field is **required** — the bot will not connect if it is empty. Any message from an unlisted user ID is silently ignored.

---

## Step 4: Choose a default agent

The **Default agent** dropdown lists every agent defined in your vault's `sidekick/agents/` folder. Select the agent that should handle incoming Telegram messages.

- Choose **Auto** to let Sidekick pick an agent based on the loaded configuration.
- Choose a specific agent (e.g. `Grammar`, `Planner`) to always route bot messages to that persona.

The selected agent determines the system prompt, allowed tools, allowed skills, and preferred model for the bot session.

---

## Step 5: Connect

Click the **Connect** button at the top of the Telegram section. Sidekick validates the token with Telegram's `getMe` API and, on success, starts long-polling for messages. You'll see:

```
Connected as @my_sidekick_bot
```

To stop the bot, click **Disconnect**.

---

## How Sidekick handles Telegram messages

Understanding the runtime behavior helps you configure it better:

### Session management

- Each Telegram chat (or **topic** in a forum group) gets its own independent Copilot session.
- Messages within the same chat/topic share conversation history automatically.
- Sending `/new` resets the session for that chat/topic.

### Message queuing

Messages in the same chat/topic are serialized — the bot finishes processing one message before starting the next. This keeps conversation history consistent.

### Typing indicator

While the bot processes a message, Sidekick sends Telegram's `typing…` indicator every 5 seconds.

### Attachments

Photos, documents, audio, voice notes, and videos sent to the bot are:

1. Downloaded via the Telegram file API.
2. Saved to `sidekick/bot-attachments/` inside your vault.
3. Passed to the Copilot session as file attachments.

### Long messages

Responses longer than 4096 characters (Telegram's limit) are automatically split at paragraph or word boundaries.

### Built-in commands

| Command | Action |
|---|---|
| `/start` | Sends a welcome message |
| `/help` | Lists available commands and features |
| `/new` | Resets the session for the current chat/topic |

---

## Using agents, skills, and tools with the bot

The bot session inherits from the same vault-local customization model as the Sidekick sidebar:

- **Agents** — defined in `sidekick/agents/*.agent.md`. The default agent controls the bot's persona.
- **Skills** — defined in `sidekick/skills/<name>/SKILL.md`. If the selected agent specifies a `skills` list, only those skills are enabled.
- **MCP tools** — defined in `sidekick/tools/mcp.json`. If the agent specifies a `tools` list, only those tool servers are available.
- **Model** — the agent's `model` frontmatter overrides the default. If using a BYOK provider, the provider model is used instead.

### Example agent for the bot

Create `sidekick/agents/assistant.agent.md`:

```markdown
---
name: Assistant
description: General-purpose assistant for Telegram
tools:
  - github
skills:
  - ascii-art
model: Claude Sonnet 4.5
---

# Assistant instructions

You answer questions concisely. When the user sends a file, summarize its contents.
```

Then select **Assistant** in the **Default agent** dropdown in settings.

---

## Tool approval

Bot sessions run headless (no UI to prompt the user), so tool calls are auto-approved regardless of the global **Tool approval** setting. Keep this in mind when choosing which tools to expose via the agent's `tools` list.

---

## BYOK providers

If you configured a BYOK provider (OpenAI, Azure, Anthropic, Ollama, Foundry Local, or another OpenAI-compatible endpoint) in the **Provider** settings tab, the bot uses that provider and model. The agent's `model` frontmatter is ignored when a BYOK provider is active.

---

## Security considerations

- The bot token is stored in Obsidian's vault-local secure storage, not in the plugin's `data.json`.
- The **Allowed users** list is the primary access control. Always populate it.
- Messages from unauthorized users are silently dropped.
- Attachments are saved to your vault — review and clean up `sidekick/bot-attachments/` periodically.
- The bot uses long-polling (outbound HTTPS only). No inbound ports or webhooks are required.

---

## Troubleshooting

| Issue | Solution |
|---|---|
| "Please add at least one allowed user ID before connecting." | Populate the **Allowed users** field with at least one numeric user ID. |
| Connection fails with a 401/Unauthorized error | Double-check the bot token. Regenerate it via `/token` in BotFather if needed. |
| Bot connects but doesn't reply | Verify your Telegram user ID is in the **Allowed users** list. Check the Obsidian developer console for errors. |
| "Session not found" errors | The bot auto-recovers by creating a fresh session. Send another message. |
| Attachments not appearing | Ensure the `sidekick/bot-attachments/` folder is writable. Large files (>20 MB) cannot be downloaded via the Telegram Bot API. |
