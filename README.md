# Johnson Mail

A self-hostable temporary mailbox / verification-code inbox system built on Cloudflare Workers, D1, KV, and Email Routing.

This repository is a **sanitized open-source packaging** of the current Johnson Mail codebase, prepared for public release without production secrets or mailbox data.

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

## License

MIT. See `LICENSE`.
