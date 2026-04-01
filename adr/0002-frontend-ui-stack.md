# ADR 0002: Frontend UI component stack

## Status

Accepted

## Context

The `fe/` package standardizes on **Headless UI** for several accessible patterns and **custom** components under `fe/src/components/ui/` for the rest, styled with **Tailwind** (class naming often mirrors shadcn-style conventions).

## Stack

| Area                         | Approach                                                                                                 |
| ---------------------------- | -------------------------------------------------------------------------------------------------------- |
| Full-screen / form modals    | `@headlessui/react` **Dialog** (fixed header/footer, single scroll body; see overlay rule)               |
| Accordion                    | Headless **Disclosure** (`fe/src/components/ui/accordion.tsx`)                                           |
| Select                       | Headless **Listbox** (`fe/src/components/ui/select.tsx`)                                                 |
| Popover / anchored panels    | Custom context + portal (`fe/src/components/ui/popover.tsx`); optional `container` for clipped ancestors |
| Composable “as child”        | `fe/src/lib/slot.tsx`                                                                                    |
| Typeahead / suggestion lists | `fe/src/components/ui/suggestion-panel.tsx`                                                              |
| Styling                      | Tailwind + shared tokens in `fe/src`                                                                     |

## Related

- Cursor rule: [04-overlays-mobile.mdc](../cursor/rules/04-overlays-mobile.mdc)
