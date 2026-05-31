# TELEGRAM_INTEGRATION.md

This document shows an **example-only** Telegram integration path for Johnson Mail.

> All values here are placeholders. Do not commit real bot tokens, production webhook URLs, or private domains.

## What Telegram integration can do

This project already contains Telegram-related code for:

- Telegram webhook handling
- Telegram mailbox binding
- Telegram mail push
- Telegram Mini App / WebApp related frontend behavior

## Example values

Use placeholders like these when documenting or testing locally:

- Bot token: `123456789:YOUR_BOT_TOKEN_HERE`
- Webhook URL: `https://mail.example.com/telegram/webhook`
- Mini App URL: `https://mail.example.com/telegram_mail`
- Test mailbox domain: `mail.example.com`

## Typical setup flow

### 1. Create a Telegram bot

Use `@BotFather` to create a bot and obtain a bot token.

Store the token as a Worker secret. Do **not** commit it.

### 2. Set Telegram-related Worker configuration

Depending on the deployment shape, you may need:

- a Telegram bot token secret
- a public Worker/custom-domain URL
- optional Mini App URL settings
- any app-specific flags used by the Telegram routes

### 3. Expose the webhook endpoint

This project includes Telegram routes under:

- `/telegram/*`

Webhook example:

- `https://mail.example.com/telegram/webhook`

### 4. Register the webhook with Telegram

Example API call:

```bash
curl -X POST "https://api.telegram.org/bot123456789:YOUR_BOT_TOKEN_HERE/setWebhook" \
  -d "url=https://mail.example.com/telegram/webhook"
```

### 5. Test bot reachability

Recommended checks:

- send `/start`
- confirm bot replies or at least reaches webhook logs
- verify mailbox binding path if your deployment uses it
- verify an incoming email can trigger Telegram push

## Example verification checklist

- [ ] Worker is deployed and public
- [ ] `/telegram/webhook` is reachable
- [ ] Telegram webhook registration succeeds
- [ ] Bot can receive `/start`
- [ ] Telegram mailbox binding works if enabled
- [ ] A real inbound email can trigger Telegram push
- [ ] The pushed message content is readable and useful

## Security reminders

- Never commit the real Telegram bot token
- Never commit production webhook URLs if they reveal private infrastructure unnecessarily
- Use secrets management, not tracked config files
- If a token was exposed in chat or logs, rotate it
