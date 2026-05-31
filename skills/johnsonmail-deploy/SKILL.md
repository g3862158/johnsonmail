---
name: johnsonmail-deploy
description: Deploy, configure, verify, or recover Johnson Mail on Cloudflare. Use when an agent needs to set up the Worker, D1, KV, domain, Email Routing, optional Telegram Bot integration, webhook push, or production verification for this repository. Best for "deploy this repo", "set up johnsonmail", "configure Telegram mail push", "wire Cloudflare Email Routing", or "restore a broken mailbox deployment" tasks.
---

# Johnson Mail Deploy

Use this skill when deploying or recovering this repository in a real Cloudflare environment.

## What this skill is for

This skill helps an agent complete the operational path, not just edit code:

- prepare Cloudflare resources,
- configure `wrangler.toml`,
- inject secrets,
- deploy Worker/frontend,
- wire Email Routing,
- optionally enable Telegram Bot / Mini App behavior,
- and verify the mailbox works end-to-end.

## Do not assume

Do **not** assume any deployment is valid until production verification passes.

At minimum, verify:

1. `/` returns the intended web UI
2. `/?jwt=...` returns the mailbox page
3. mailbox creation path behaves as intended for the selected auth model
4. a real inbound email reaches the inbox
5. parsed mail APIs return the message
6. if Telegram is enabled, Telegram push works

## Required inputs from the human operator

Before acting, obtain or confirm:

- Cloudflare account access
- target domain or subdomain
- whether this is a new deployment or a restore/recovery
- whether anonymous mailbox creation should be enabled
- whether Telegram Bot integration should be enabled
- whether webhook delivery should be enabled

## Required Cloudflare resources

Typical minimum resources:

- 1 Cloudflare Worker
- 1 D1 database
- 1 KV namespace
- 1 domain / route / custom domain
- Email Routing on the receiving domain

Optional depending on feature set:

- Pages deployment
- R2 / S3-compatible attachment storage
- Workers AI related settings

## Deployment order

Follow this order unless there is a strong reason not to.

### 1) Prepare config from template

Start from:

- `deploy/wrangler.toml.example`

Fill in:

- Worker name
- D1 binding and database id
- KV binding and namespace id
- target domain values
- title / feature flags / domain lists

Do not commit real production ids or secrets if the repo is public.

### 2) Set secrets out of git

Use Wrangler secrets for items such as:

- JWT secret
- admin passwords
- Telegram Bot token
- any SMTP / Resend / third-party keys

Never store those in tracked files.

### 3) Configure mailbox domains

Confirm the intended domain lists are set consistently.

When modifying domain pools, check all relevant places such as:

- default domains
- normal domains
- random-subdomain domains
- any Telegram-side fallback domain pool if present

### 4) Deploy Worker

Deploy the Worker only after bindings and vars are coherent.

If the deployment includes static assets, verify the asset directory is the intended one. Do not casually deploy broken or placeholder assets.

### 5) Configure Email Routing

For inbound mail to work, Cloudflare Email Routing must deliver to the Worker.

Check:

- receiving domain is enabled for Email Routing
- matchers target the intended addresses/domains
- rules route to the Worker, not a dead destination

Important: treat matcher semantics carefully. Do not assume wildcard behavior unless verified.

### 6) Configure Telegram integration if requested

If Telegram support is desired, verify:

- Telegram-related vars/secrets are set
- `/telegram/webhook` is reachable
- webhook registration is correct
- Telegram Mini App / WebApp URL is correct if used
- mailbox binding and mail push paths work

### 7) Run end-to-end verification

Use a real mailbox and a real inbound message.

Minimum verification:

- open `/`
- open `/?jwt=...`
- create or access a mailbox
- send a real test email
- confirm inbox API returns the message
- confirm parsed mail view returns the message
- confirm UI shows the message
- if Telegram enabled, confirm Telegram receives it



## Hard requirements for every update

These are mandatory operating rules for this repository.

### 1) Every update must carry a version trail

After each meaningful change or deployment:

- record or bump a version marker,
- keep a clear changelog trail,
- make rollback targets discoverable.

Minimum expectation:

- version identifier or deploy label,
- what changed,
- when it changed,
- what the previous known-good version was.

If the environment supports deploy versions, capture the deploy version id. If the repo uses release/version files, update them consistently.

### 2) Every deployment must verify main functionality, not just the changed part

Do **not** deploy changes by only checking the local diff or the newly modified path.

Before calling the update successful, verify the main user-facing functionality still works.

For Johnson Mail, main-function verification usually includes:

1. homepage/root UI works
2. direct mailbox page `/?jwt=...` works
3. mailbox access/creation path still works under the intended auth model
4. inbound email can still arrive
5. parsed mail API still returns usable content
6. if enabled, Telegram/webhook delivery still works

If the change touched one area but broke the main flow, the deployment is not acceptable.

### 3) Persist user preferences and learned constraints

When a user expresses a repeatable preference, requirement, formatting rule, or operational lesson:

- write it down,
- do not rely on session memory,
- update the relevant durable file or skill.

Examples:

- output formatting requirements
- deployment safety rules
- rollback discipline
- verification requirements
- domain/addition constraints
- style or UX expectations

This is part of adaptive agent behavior. Learning is not complete until it is persisted.

## Common failure patterns to avoid

These are not theoretical. They are recurring operational mistakes that waste time and cause regressions.

### 1) Treating source rebuilds as the same thing as known-good production

Do **not** assume that rebuilding the source tree reproduces a historical production deployment exactly.

If the user wants:

- a specific old UI,
- a known-good behavior,
- or “restore it to that version”,

then the first move should be:

1. identify the known-good deployed version or artifact,
2. restore that baseline,
3. only then apply the smallest necessary delta.

### 2) Mixing multiple problem classes together

Keep these concerns separate:

- root web UI (`/`)
- direct mailbox page (`/?jwt=...`)
- mailbox/domain pool configuration
- inbound delivery chain (Email Routing -> Worker -> DB -> API)
- Telegram push / webhook delivery

If these are mixed together, agents tend to “fix” one layer while breaking another and then lose the causal chain.

### 3) Stacking hotfixes on top of a broken state

Do **not** keep layering more changes onto a deployment that is already in a bad or ambiguous state.

Preferred rule:

1. stop,
2. restore the last known-good deployment,
3. change one thing,
4. verify,
5. only then continue.

### 4) Assuming Email Routing matcher semantics without proof

Do **not** assume a matcher value like `*@example.com` behaves as a wildcard unless the actual matcher type supports that behavior and you verified it.

Safer debugging pattern:

- first make one exact mailbox rule work,
- then generalize only after proof.

### 5) Declaring success after partial checks

A deployment is **not fixed** just because:

- homepage returns 200,
- Worker deploy succeeded,
- a config endpoint looks correct,
- or one layer of the flow works.

Success must be based on the full intended path.

## Non-negotiable verification discipline

After every meaningful change, verify the relevant end-to-end path.

For a mailbox deployment, that usually means:

1. `/` returns expected UI
2. `/?jwt=...` returns expected mailbox view
3. intended mailbox creation/access path works
4. inbound email actually arrives
5. parsed mail API returns it
6. UI displays it
7. Telegram/webhook push works if enabled

If one item fails, do **not** report success.

## Recovery mindset for agents

When repeated attempts are happening, the agent should explicitly say:

- what is currently known-good,
- what is currently broken,
- what single variable is being changed next,
- and what exact verification will decide the next step.

This is how to avoid endless guess-and-check loops.

## Recovery / rollback workflow

If the deployment is broken:

1. determine whether the failure is:
   - UI/assets,
   - direct mailbox page,
   - domain config,
   - or delivery chain
2. stop stacking unrelated changes
3. restore the last known-good deploy first
4. then apply the smallest necessary delta
5. re-run full verification

## Agent-facing practical rule

If a user says something like:

- “把这个项目丢给龙虾让它部署”
- “deploy this repo for me”
- “set up Telegram mail push”
- “wire Cloudflare Email Routing”

the agent should not stop at code inspection. It should drive the full deployment checklist and explicitly report which external inputs are still missing.

## Recommended companion docs

When using this skill, also read:

- `README.md`
- `DEPLOYMENT_FOR_AGENTS.md`
- `SECURITY.md`
- `OPEN_SOURCE_NOTES.md`

## Definition of done

This task is only done when:

- deployment succeeded,
- production endpoints respond correctly,
- a real inbound test message is visible in the inbox,
- and optional Telegram/Webhook integrations were verified if requested.
