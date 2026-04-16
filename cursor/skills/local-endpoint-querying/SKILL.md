---
name: local-endpoint-querying
description: Hit local site-index API endpoints with curl, including login and tenant headers. Use when debugging FE data loading, checking endpoint payloads, validating auth/studio scope, or when the user asks to inspect local endpoint responses.
---

# Local Endpoint Querying (site-index)

## Scope

Use this skill to verify what the frontend receives from local backend endpoints.

## Local API + credentials

- Base URL: `http://localhost:3000/v1`
- Health check: `GET /health`
- Login credentials (local dev):
  - Email: `fseitun@gmail.com`
  - Password: `123123123`
  - Studio slug: `mecarq`

## Standard flow

1. Login:

```bash
curl -sS -X POST "http://localhost:3000/v1/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email":"fseitun@gmail.com","password":"123123123","studioSlug":"mecarq"}'
```

2. Extract `accessToken` from the response.

3. Call tenant-scoped endpoints with both:
   - `Authorization: Bearer <token>`
   - `X-Studio-Slug: mecarq`

Example:

```bash
curl -sS "http://localhost:3000/v1/studio-catalog-items" \
  -H "Authorization: Bearer <token>" \
  -H "X-Studio-Slug: mecarq"
```

## High-value endpoint checks

- `GET /projects`
- `GET /work-categories`
- `GET /studio-catalog-items`
- `GET /projects/:projectId/budget-lines`
- `GET /projects/:projectId/item-yields`

## Querying rules

- Prefer direct endpoint verification over assumptions from code.
- When sandbox blocks localhost access, request `full_network`.
- Report:
  - HTTP status,
  - key counts (`length`),
  - critical IDs (studio slug, project ID, category ID),
  - a short sample payload (first 1-2 rows).
