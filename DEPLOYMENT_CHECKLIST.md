# DEPLOYMENT_CHECKLIST.md

This checklist is intentionally example-driven and public-safe.

## 0. Inputs you need before deployment

- [ ] Cloudflare account access
- [ ] A domain or subdomain for the mailbox service
- [ ] A D1 database
- [ ] A KV namespace
- [ ] A Worker name
- [ ] A JWT secret
- [ ] Admin password(s)
- [ ] Email Routing access for the target domain
- [ ] Optional Telegram Bot token

## 1. Prepare config from template

Start from:

- `deploy/wrangler.toml.example`

Fill placeholder values such as:

- Worker name: `johnsonmail`
- Domain example: `mail.example.com`
- D1 database id: `replace-me`
- KV namespace id: `replace-me`

## 2. Set secrets out of git

Use Worker secrets for values such as:

- `JWT_SECRET`
- admin passwords
- Telegram bot token
- any third-party mail/send credentials

## 3. Verify domain and routing plan

- [ ] Decide which mailbox domain(s) to support
- [ ] Confirm Worker route/custom domain
- [ ] Confirm Email Routing target rule design
- [ ] Confirm whether wildcard matching is actually supported by the matcher type you use

## 4. Deploy

- [ ] Deploy the Worker
- [ ] If using static assets, verify the asset directory before deploy
- [ ] Avoid deploying placeholder or broken assets

## 5. Configure Email Routing

Example target behavior:

- inbound mail to `user@mail.example.com`
- routed by Cloudflare Email Routing
- delivered into the Worker mailbox flow

Verify:

- [ ] domain Email Routing is enabled
- [ ] rule exists and targets the Worker
- [ ] test mailbox address is covered by the rule

## 6. Optional Telegram enablement

If Telegram is required:

- [ ] set Telegram-related secret(s)
- [ ] expose `/telegram/webhook`
- [ ] register webhook with Telegram
- [ ] verify Telegram can receive pushed mail events

## 7. Minimum end-to-end verification

These checks should pass before calling deployment done:

- [ ] `/` returns the intended web UI
- [ ] `/?jwt=...` returns the mailbox page
- [ ] mailbox creation/access path works for the chosen auth model
- [ ] a real inbound email reaches the inbox
- [ ] `/api/parsed_mails` returns the email
- [ ] the web UI can display the email
- [ ] Telegram push works if enabled
- [ ] webhook delivery works if enabled

## 8. Recovery discipline

If something breaks:

- [ ] identify whether it is UI, direct-mailbox page, config, or delivery-chain failure
- [ ] stop stacking unrelated changes
- [ ] restore last known-good deployment first
- [ ] then apply the smallest required fix
- [ ] re-run the full verification checklist

## 9. Frozen baseline / do-not-roll checklist

For every real deployment environment, keep a **frozen known-good baseline** outside secrets but near operations docs.

Recommended contents:

- [ ] known-good deploy/version id
- [ ] expected `/` markers
- [ ] expected `/?jwt=...` markers
- [ ] expected `/open_api/settings` values
- [ ] expected domain arrays
- [ ] rollback target
- [ ] last verified timestamp

When asked to restore a historical good state:

- [ ] do **not** assume a fresh source rebuild is equivalent
- [ ] do **not** use generic repo UI descriptions or the default frontend as the environment's restore target
- [ ] restore the known-good deployment/artifact first
- [ ] verify `/` and `/?jwt=...` separately
- [ ] verify `/open_api/settings` separately
- [ ] only then apply the requested small delta

When asked to "keep current jwt UI, only patch settings":

- [ ] preserve/mirror the currently working assets first
- [ ] keep the current direct-mailbox Worker/UI path unchanged
- [ ] patch only settings/domain vars
- [ ] redeploy
- [ ] re-check `/`, `/?jwt=...`, and `/open_api/settings`
