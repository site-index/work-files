# fc-be — full check (backend)

## When to use

Use as the **final step** before considering backend work complete. If anything fails, fix the issues and run this again until everything passes.

## Command

Run from the **backend package root** (`be/`, the directory containing `be/package.json`):

```bash
cd be && npm run full-check
```

From the workspace root (`site-index`):

```bash
npm run full-check --prefix be
```

## Notes

- Do not treat the task as done until the command exits with status 0.
- If the change also touches `fe/`, run `fc-fe` (or `npm run full-check --prefix fe`) as well.
