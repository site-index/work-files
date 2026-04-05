# fc-fe — full check (frontend)

## When to use

Use as the **final step** before considering frontend work complete. If anything fails, fix the issues and run this again until everything passes.

## Command

Run from the **frontend package root** (`fe/`, the directory containing `fe/package.json`):

```bash
cd fe && npm run full-check
```

From the workspace root (`site-index`):

```bash
npm run full-check --prefix fe
```

## When only a few files are flagged

Format specific frontend files from the workspace root:

```bash
npm run format:files --prefix fe -- src/components/CreateBudgetLineDialog.tsx src/components/SidebarContent.tsx src/pages/budget-lines/helpers.ts
```

You can replace those paths with any frontend file list. Prefer this script over raw `npx prettier` when your current directory may not be `fe/`.

## Notes

- Do not treat the task as done until the command exits with status 0.
- If the change also touches `be/`, run `fc-be` (or `npm run full-check --prefix be`) as well.
