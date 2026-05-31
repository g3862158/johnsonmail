# Open Source Notes

This repository is a sanitized, self-hostable packaging of the current Johnson Mail codebase.

## What was removed

- Production API tokens and keys
- Real JWT secrets
- Admin passwords
- Mailbox JWT links
- Production-only Worker artifacts
- Cloudflare-specific live identifiers where not necessary for the code structure
- Personal operational memory files and private workspace notes

## What remains intentionally

- The deployable source tree
- Frontend / worker / database source tree from the upstream MIT-licensed project base
- Example deployment templates and security guidance

## Operator warning

This project may require Cloudflare Email Routing, D1, KV, and optional assets configuration to work in production.
