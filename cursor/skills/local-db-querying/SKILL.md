---
name: local-db-querying
description: Query the local PostgreSQL database for the site-index workspace using psql quick checks. Use when debugging local data, confirming seed results, checking project/catalog/studio rows, or when the user asks to inspect the local DB.
---

# Local DB Querying (site-index)

## Scope

Use this skill for this workspace when the task needs direct local DB verification instead of code-only assumptions.

## Connection

- Local DB URL format in this project: `postgresql://siteindex:siteindex@localhost:5433/siteindex`
- If running from tools that sandbox network, request `full_network` for localhost DB access.
- Prefer `psql` for fast checks.
