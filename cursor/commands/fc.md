# fc — full check (both packages)

## When to use

Use as the **final step** before considering work complete when changes may span **frontend and/or backend**. Run the checks that apply; if anything fails, fix and repeat until exit status 0.

## Commands

**Frontend** (`fe/`):

```bash
npm run full-check --prefix fe
```

**Backend** (`be/`):

```bash
npm run full-check --prefix be
```

If you only changed one side, you can run just that package’s command (see `fc-fe` / `fc-be`).

## Notes

- Run from the **workspace root** (`site-index`), i.e. the parent of `fe/` and `be/`.
- Do not treat cross-cutting work as done until every affected package’s `full-check` passes.
