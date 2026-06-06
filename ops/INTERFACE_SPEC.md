# Interface Specification

## Canonical default mailbox UI

Unless an environment-specific baseline explicitly says otherwise, Johnson Mail's **canonical default mailbox UI** is the tabbed mailbox interface backed by the normal frontend shell.

This default UI includes these concepts/features:

- address management entry (`地址管理` / Address Management)
- mailbox tab (`收件箱`)
- account settings tab (`账户` / Account Settings)
- appearance tab (`外观` / Appearance)
- current-page filter (`过滤当前页`)
- auto-refresh controls (`自动刷新` / refresh interval)
- normal root/frontend shell at `/`
- direct mailbox access via `/?jwt=...`

## Non-default / compatibility UI modes

The repository may still include alternate or compatibility UI paths such as:

- simple mode / minimalist mode
- historical mailbox URL renderings
- environment-specific restored direct-mailbox pages

These are **not** the canonical default UI specification.

They should be treated as one of the following only:

1. optional compatibility mode,
2. historical version trail,
3. environment-specific recovery target recorded in a baseline document.

## Rules

- Do **not** describe multiple UI variants as equal default targets in deployment/recovery docs.
- Do **not** use historical UI descriptions as generic repo-wide restore goals.
- Do **not** remove compatibility UI code unless explicitly requested and impact-reviewed.
- If a real production environment needs a different restore target, define that in its own baseline file, not here.

## Practical interpretation for agents/operators

When editing docs, deployment SOPs, or recovery guides:

- keep only this canonical default UI as the repo-wide interface description
- downgrade older UI descriptions to historical notes or compatibility notes
- preserve version history in changelogs, but do not let changelog history override the current default interface spec
