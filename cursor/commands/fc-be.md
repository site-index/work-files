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

## When only a few files are flagged

Format specific backend files from the workspace root:

```bash
npm run format:files --prefix be -- src/common/duration-ms.ts src/main.ts src/app.module.ts
```

You can replace those paths with any backend file list. Prefer this script over raw `npx prettier` when your current directory may not be `be/`.

## Notes

- Do not treat the task as done until the command exits with status 0.
- If the change also touches `fe/`, run `fc-fe` (or `npm run full-check --prefix fe`) as well.
