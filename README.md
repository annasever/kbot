
# Kbot — Telegram Bot CLI Application in Go

Kbot is a simple Telegram bot built in Go using the telebot library and managed with the [Cobra](https://github.com/spf13/cobra) CLI framework. It demonstrates how to create a Telegram bot command that responds to simple payload messages.

---

## Features

- Uses Cobra to create a CLI command `kbot` with an alias `start`.
- Connects to Telegram Bot API using a token from the `TELE_TOKEN` environment variable.
- Listens for text messages and reacts to a `"hello"` payload with a friendly response.
- Logs incoming message payload and text for easy debugging.
- Easily extensible to handle additional commands and payloads.

---

## Prerequisites

- Go 1.18+ installed.
- A Telegram bot token. You can create a bot via [@BotFather](https://t.me/BotFather) on Telegram.
- Set your bot token in the environment variable `TELE_TOKEN`.

```bash
export TELE_TOKEN="your_telegram_bot_token_here"
```

---
## Bot Link

Interact with the bot at: [t.me/annasever22_bot](https://t.me/annasever22_bot)

## Installation & Usage

1. Clone the repository:

```bash
git clone https://github.com/annasever/kbot.git
cd kbot
```
2.  Install Cobra CLI tool:
```
go install github.com/spf13/cobra-cli@latest 
```

3. Set up the bot token (obtained from BotFather)
```
export TELE_TOKEN=<your_bot_token>
```

4. Build the bot with a version flag:

```bash
go build -ldflags "-X github.com/annasever/kbot/cmd.appVersion=v1.0.2"
```

5. Run the bot with the `start` command:

```bash
./kbot start
```

You should see output similar to:

```
kbot v1.0.2 started
```

---

## How to Use

- Send the Telegram bot a message with the text `/start hello` or `hello` as payload.
- The bot will reply with a greeting message including its version, e.g.:

```
Hello I'm Kbot v1.0.2!
```

- Any other payloads are ignored silently.

---