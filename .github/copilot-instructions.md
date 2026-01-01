# Nautilus Wallet — Copilot instructions

This file gives concise, actionable guidance for AI coding agents working on Nautilus Wallet.

- **Project type:** Vue 3 webextension (multiple entry points) built with Vite and `vite-plugin-web-extension`. See [vite.config.ts](vite.config.ts#L1-L120) and the manifest builder [src/extension/manifest.ts](src/extension/manifest.ts#L1-L120).

- **Local dev & builds:**
  - Install: `pnpm i` (project uses `pnpm` and patched deps in `patches/`). See [package.json](package.json#L1-L80).
  - Dev (Chrome): `pnpm run dev:mainnet` (default). For testnet: `pnpm run dev:testnet`.
  - Build targets: `pnpm run build:mainnet:chrome`, `build:mainnet:firefox`, and testnet variants. Use `INSPECT=true pnpm run build:mainnet:chrome` to open rollup visualizer (`build:inspect`).
  - Docs: `pnpm run dev:docs` / `pnpm run build:docs` (vitepress).

- **Important env vars:**
  - `NETWORK` = `mainnet` | `testnet`
  - `TARGET` = `chrome` | `firefox`
  - `INSPECT` = `true` to enable bundle visualizer
  - These are wired into `vite.config.ts` and `src/extension/manifest.ts` — do not hardcode values.

- **Extension structure & entry points:**
  - Extension sources live under `src/extension`. Manifest is generated at build-time by `buildManifest` ([src/extension/manifest.ts](src/extension/manifest.ts#L1-L140)).
  - Connector UI and RPC logic live in `src/extension/connector/` (see [protocol constants](src/extension/connector/rpc/protocol.ts#L1-L80)). Use these constants when adding dApp RPC messages.
  - `vite-plugin-web-extension` additional inputs include `injected.ts` and the connector HTML; check `vite.config.ts` for details.

- **Runtime / platform notes:**
  - Development mode is primarily supported in Chrome (Firefox dev has historical issues — see README). Be cautious when changing browser-specific manifest fields; prefer `buildManifest` modifications.
  - Ledger/crypto code requires node/browser polyfills; Vite uses `vite-plugin-node-polyfills` (see `vite.config.ts`).

- **Data flows & conventions:**
  - Client state is managed with Pinia stores in `src/stores/`. Some stores use a private internal store pattern (e.g. `_wallet` in [src/stores/walletStore.ts](src/stores/walletStore.ts#L1-L30)). Follow that pattern when adding complex store state.
  - Persistent storage uses Dexie via `src/database/dbContext.ts` and DB services in `src/database/*DbService.ts`. Migrations are in `dbContext.ts` — prefer schema changes via Dexie version upgrades.
  - Connector protocol and RPC namespace are centralized in [src/extension/connector/rpc/protocol.ts](src/extension/connector/rpc/protocol.ts#L1-L120). Reuse the `RPC_NAMESPACE`, `ExternalRequest` and `InternalRequest` enums.

- **Tests & quality checks:**
  - Unit tests: `pnpm run test:unit` (vitest) — tests are under `tests/unit/`.
  - Lint: `pnpm run test:lint` (ESLint). Format check: `pnpm run test:format`. Use `pnpm run fix:format` to write fixes.

- **Dependency & patching conventions:**
  - `pnpm` patched dependencies are in `patches/` and declared in `package.json` under `pnpm.patchedDependencies`. Do not remove or reintroduce a patched package without validating the patch.

- **When changing the extension manifest or build:**
  - Edit `buildManifest` in [src/extension/manifest.ts](src/extension/manifest.ts#L1-L140). The manifest is derived from `package.json` version and env; keep the manifest generator canonical to avoid divergent packaging.

- **Quick code patterns to follow (examples):**
  - Use exported constants/types instead of string literals for RPC and errors: see [protocol.ts](src/extension/connector/rpc/protocol.ts#L1-L120).
  - Use DB services for read/write rather than direct Dexie table access (see `addressesDbService.ts`, `assetsDbService.ts`).
  - For new UI, follow existing component layout in `src/components/` and connector views in `src/extension/connector/views/`.

If anything in this summary is unclear or you'd like more detail (more file links, examples, or a checklist for PRs), tell me which sections to expand. 
