# How to create a Telegram bot for Sidekick

This guide walks through creating a Telegram bot using BotFather and obtaining the credentials Sidekick needs.

---

## Prerequisites

- A Telegram account (mobile or desktop app).
- Obsidian with the Sidekick plugin installed.

---

## Step 1: Start a conversation with BotFather

1. Open Telegram and search for **@BotFather** (the official bot-management bot from Telegram).
2. Tap **Start** or send `/start`.

BotFather will reply with a list of available commands.

---

## Step 2: Create a new bot

1. Send `/newbot` to BotFather.
2. BotFather asks for a **display name** — type a human-readable name, for example `My Sidekick`.
3. BotFather then asks for a **username**. This must end in `bot` (e.g. `my_sidekick_bot`). This username is globally unique on Telegram.

Once created, BotFather replies with:

- The bot's **username** (e.g. `@my_sidekick_bot`).
- The **bot token** — a string like `123456789:ABCdefGHI-jklMNOpqrSTUvwxYZ`. Keep this secret.

---

## Step 3: Note down your credentials

You need two pieces of information for the Sidekick settings:

| Field | Where to find it | Example |
|---|---|---|
| **Bot identifier** | The numeric part before the `:` in the token, or the username | `123456789` |
| **Bot token** | The full token string from BotFather | `123456789:ABCdefGHI-jklMNOpqrSTUvwxYZ` |

---

## Step 4: Get your Telegram user ID

Sidekick requires an explicit allowlist of Telegram user IDs that can interact with the bot. This prevents unauthorized access to your vault through the bot.

To find your user ID:

1. Search for **@userinfobot** on Telegram and start a conversation.
2. It will reply with your numeric user ID (e.g. `987654321`).

Alternatively, forward any message to @userinfobot and it will show the sender's ID.

Repeat this for anyone else you want to authorize.

---

## Step 5: Optional — enable topics (forum mode)

If you want to run multiple parallel conversations with your bot using Telegram topics:

1. Create a **group** or **supergroup** on Telegram.
2. Add your bot as a member (search for its username and add it).
3. Enable **Topics** in the group settings (under **Group type** or **Permissions**, depending on your client).

Each topic becomes an independent Sidekick session, letting you discuss different subjects simultaneously.

---

## Step 6: Optional — set bot commands

You can register slash commands with BotFather so they appear in Telegram's command menu:

1. Send `/setcommands` to BotFather.
2. Select your bot.
3. Send the following text:

```
start - Start a new conversation
new - Reset the current session
help - Show available commands
```

---

## Next steps

Proceed to the [Telegram bot configuration guide](telegram-bot-config-guide.md) to connect the bot to Sidekick inside Obsidian.
