# Johnson Mail

> 中文简介：Johnson Mail 是一个可自部署的临时邮箱 / 验证码邮箱系统，基于 Cloudflare Workers、D1、KV 和 Email Routing 构建。它适合做验证码收取、一次性邮箱、Telegram 收件提醒、Webhook 转发，以及面向 AI agent 的邮箱自动化消费。

A self-hostable temporary mailbox / verification-code inbox system built on Cloudflare Workers, D1, KV, and Email Routing.

This repository is a **sanitized open-source packaging** of the current Johnson Mail codebase, prepared for public release without production secrets or mailbox data.

## Why this repo exists

This repo is meant to be a cleaner public starting point for people who want to:

- deploy a temporary mailbox service on Cloudflare,
- receive verification emails in a web inbox,
- push incoming mail to Telegram or other webhook targets,
- or hand the project to an AI agent / operator and have them finish deployment with a reproducible checklist.

## 中文说明

这个仓库不是线上生产环境的原样导出，而是一个**做过脱敏和公开整理**的版本：

- 不包含生产 token / JWT / 管理密码
- 不包含私人邮箱数据
- 不包含私人运维记忆文件
- 保留了可以继续维护、部署、二次开发的源码结构

如果你想把这个项目直接丢给“龙虾”或其他 AI agent 去部署，这个仓库已经具备基础条件，但还需要配合：

- Cloudflare 账号与 API 权限
- D1 / KV / 自定义域名 / Email Routing
- Worker secrets
- 一份清晰的部署 SOP 或 agent skill

换句话说：**它已经接近“可交给 agent 完成部署”，但还不是零配置一键自动化。**

## Status

- Preserves the current mailbox web experience pattern used in production restoration work
- Includes the upstream source tree needed for deeper maintenance and future clean rebuilds
- Excludes all live secrets, tokens, JWTs, private mailbox data, and operator-only notes

## Provenance

This project is based on the MIT-licensed upstream project:

- Upstream: `dreamhunter2333/cloudflare_temp_email`
- License: MIT

Johnson Mail adds operator packaging, deployment notes, and a safer open-source packaging workflow learned from production incident handling.

## Repository Layout

- `worker/` – Worker/backend source
- `frontend/` – Web UI source
- `db/` – D1 schema and migrations
- `mail-parser-wasm/` – Rust WASM mail parser
- `smtp_proxy_server/` – Optional SMTP/IMAP proxy helpers
- `deploy/wrangler.toml.example` – sanitized deployment template
- `skills/cf-temp-mail-agent-mail/` – agent-facing mailbox skill from the upstream project base

## Telegram / Bot / Webhook integration status

This project already contains substantial Telegram-related integration surface:

- Worker Telegram routes under `/telegram/*`
- Telegram webhook endpoint at `/telegram/webhook`
- Telegram Mini App related frontend logic
- Admin-side Telegram settings pages / APIs
- Mail push to Telegram users
- Generic webhook delivery (including Telegram Bot API, Discord, WeCom examples)

So the answer is **yes**: the project already has Telegram/Bot/Webhook interfaces.

But the answer to “can someone throw this repo to 龙虾 and have deployment fully completed automatically?” is currently:

- **Partially yes for engineering capability**
- **Not yet yes for operator UX**

What is still missing for that workflow is mainly packaging and instructions, not core capability:

- a concise Chinese deployment SOP,
- an agent-oriented deployment skill,
- a minimal required env/binding checklist,
- a post-deploy verification checklist,
- and a documented “Telegram-enabled deployment path”.

## Agent / 龙虾 deployment readiness

If you want this repo to become truly “drop it to 龙虾 and let it deploy”, the next practical step is to add a dedicated deployment skill / runbook that tells the agent exactly:

1. what Cloudflare resources must exist,
2. which secrets must be injected,
3. which Wrangler config to start from,
4. how to configure Email Routing,
5. how to enable Telegram Bot mode,
6. and how to verify `/`, `/?jwt=...`, `/api/new_address`, and Telegram push end-to-end.

That is much more important than adding more code right now.

## Security / Privacy

Before deploying, you must provide your own:

- Cloudflare API credentials
- D1/KV bindings
- JWT secret
- admin passwords
- Email Routing rules
- domain configuration

Read:

- `SECURITY.md`
- `OPEN_SOURCE_NOTES.md`

## Important Operational Lessons

This codebase has one major operational pitfall:

- **Historical production behavior and source-tree behavior may differ.**

If your goal is to restore a specific historical UI/behavior in your own deployment, do not assume a fresh rebuild is identical. Keep your own deployment history and rollback path.

## Deployment Notes

1. Start from `deploy/wrangler.toml.example`
2. Add real bindings and environment values
3. Add secrets via Wrangler secrets, not git
4. Configure Cloudflare Email Routing separately
5. Verify both:
   - `/`
   - `/?jwt=...`
6. Test real mail delivery end-to-end

## Recommended next step

If you want, the next best improvement is **not** more feature code.
It is to add:

- `README.zh-CN.md` or a stronger bilingual README,
- a dedicated `DEPLOYMENT.md`,
- and a `skill` specifically for AI-agent / 龙虾 deployment.

That would make this repo much more reusable and much closer to “handoff to an agent and deploy successfully”.

## License

MIT. See `LICENSE`.
