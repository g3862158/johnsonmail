# Known Good Baseline (Example)

> Copy this file per real deployment environment, fill it in, and keep it updated after every verified good release.
> Do not commit secrets here.

## Environment

- Name: production-example
- Worker URL / domain: https://mail.example.com
- Last verified: YYYY-MM-DD HH:mm TZ
- Operator: your-name

## Known-good deployment

- Worker version / release id: replace-me
- Frontend/assets release marker: replace-me
- Rollback target: replace-me

## Expected gates

### 1) Root page `/`

Must satisfy:

- HTTP 200
- expected UI shell / asset markers:
  - replace-me
- must NOT contain:
  - placeholder marker A
  - temporary rescue marker B

### 2) Direct mailbox page `/?jwt=...`

Must satisfy:

- HTTP 200
- expected mailbox markers:
  - replace-me
  - replace-me
  - replace-me
- must NOT contain:
  - placeholder marker A
  - temporary rescue marker B

### 3) Settings `/open_api/settings`

Expected:

- title: replace-me
- version: replace-me
- defaultDomains:
  - replace-me
- domains:
  - replace-me
- randomSubdomainDomains:
  - replace-me

## Functional checks

- mailbox create/access path: expected behavior here
- real inbound email: expected behavior here
- parsed mail API: expected behavior here
- Telegram/webhook (if enabled): expected behavior here

## Safe recovery order

1. restore known-good deploy/artifact first
2. verify `/`
3. verify `/?jwt=...`
4. verify `/open_api/settings`
5. only then apply the requested delta

## Safe "keep current jwt UI, only patch settings" order

1. preserve/mirror currently working assets first
2. keep current direct-mailbox UI path unchanged
3. patch settings/domain vars only
4. redeploy
5. re-verify `/`, `/?jwt=...`, `/open_api/settings`
