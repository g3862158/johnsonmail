# Security Policy

## Supported Use

This project is intended for self-hosted temporary mail / mailbox workflows on infrastructure you control.

## Sensitive Data

Do not commit or publish:

- Cloudflare API tokens
- Worker secrets
- JWT secrets
- admin passwords
- production mailbox contents
- real user credentials
- exported D1/KV/R2 data

Use environment variables, Wrangler secrets, and per-environment configuration files that stay out of git.

## Reporting a Vulnerability

If you discover a security issue in this repository, please report it privately to the repository owner before public disclosure.
