# DEPLOYMENT_FOR_AGENTS.md

This file is for AI agents / operators who need to deploy Johnson Mail with minimal guesswork.

## Goal

Deploy a working mailbox service that supports:

- web inbox,
- JWT mailbox links,
- Cloudflare Email Routing delivery,
- optional Telegram Bot integration,
- optional webhook push.

## Required external resources

Before deployment, the human operator must provide or approve:

1. A Cloudflare account
2. A domain (or subdomain) for the mailbox service
3. Cloudflare D1 database
4. Cloudflare KV namespace
5. Worker secrets / env vars
6. Email Routing setup on the target domain
7. Optional Telegram Bot token if Telegram integration is desired

## Minimum deployment checklist

1. Copy `deploy/wrangler.toml.example`
2. Fill in real Worker name, D1, KV, and domain values
3. Set secrets with Wrangler, not in git:
   - JWT secret
   - admin passwords
   - optional Telegram Bot token
4. Deploy the Worker
5. Configure Cloudflare Email Routing to send mail to the Worker
6. If Telegram is needed:
   - configure Telegram-related worker vars
   - configure webhook path `/telegram/webhook`
   - configure optional Telegram Mini App URL if used

## Minimum verification checklist

After deployment, verify:

1. `/` returns the expected web UI
2. `/?jwt=...` returns the expected mailbox page
3. `/api/new_address` or `/admin/new_address` behaves as intended for your auth model
4. Email Routing delivers a real message into the inbox
5. `/api/parsed_mails` returns the received message
6. If Telegram is enabled, the Telegram push path works end-to-end
7. If webhook is enabled, webhook test / delivery works end-to-end

## Known Telegram-related code surface

The repository already includes:

- `/telegram/webhook`
- Telegram mailbox binding APIs
- Telegram Mini App frontend logic
- admin Telegram settings
- Telegram push support in the mail handling flow
- webhook presets for Telegram Bot API

## Known caveat

Do not assume source-tree behavior always exactly matches a historical production deployment. If restoring an older production experience matters, keep your own rollback/version history.

## Recommendation

If this project is going to be routinely handed to AI agents (such as 龙虾), create a dedicated deployment skill that includes:

- exact env var names,
- recommended defaults,
- Telegram enablement steps,
- Email Routing examples,
- post-deploy test commands,
- rollback guidance.
