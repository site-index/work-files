# ADR 0001: PWA update strategy and install icons

## Status

Accepted

## Context

SITE INDEX ships as a **mobile-first** browser PWA (`fe/`, Vite + `vite-plugin-pwa`). Service workers control caching of the app shell. We need a predictable way to **suggest** updates without trapping users in modals, and install surfaces that work on **iOS and Android**.

## Decision

1. **Update UX:** Use **`registerType: 'prompt'`** with **`injectRegister: false`**. Registration and refresh are driven by **`useRegisterSW`** (`virtual:pwa-register/react`). When a new service worker is waiting, show a **non-modal bottom bar** (`PwaUpdateBanner`) with Spanish (es-AR) copy; **Actualizar** calls `updateServiceWorker()` (reload after activation); **Más tarde** dismisses the bar without activating the waiting worker.
2. **Forced upgrades:** Breaking API or schema changes are **out of scope** for this ADR. If needed later, add a **separate** minimum client/API version check and a blocking screen; do not rely on the PWA bar alone for security or data-integrity gates.
3. **Install icons:** Ship **PNG** assets for manifest and Apple touch icon (`pwa-192.png`, `pwa-512.png`, `apple-touch-icon.png`), generated from [`fe/public/pwa-icon.svg`](../../fe/public/pwa-icon.svg). SVG-alone icons are insufficient for reliable **Add to Home Screen** behavior on some platforms (notably iOS).

## Consequences

- Deploys do not auto-activate a new worker until the user taps **Actualizar** (or leaves the app and returns, per browser lifecycle). Slightly older clients may coexist until users refresh; acceptable for current online-first data model.
- After changing the brand mark, regenerate PNGs from `pwa-icon.svg` and smoke-test **Add to Home Screen** on a real iPhone and Android device.

## Alternatives considered

- **`registerType: 'autoUpdate'`** — Lower friction but can surprise users mid-task on mobile; rejected as default for this product.
- **Modal / full-screen update gate** — Stronger push to refresh; rejected as the default UX to align with non-intrusive operational prompts; reserved for a future API-version gate if required.
