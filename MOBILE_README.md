# Mobile (Android) — Quick start

This project uses Capacitor to wrap the existing Vite-built web app into a native Android project you can open in Android Studio.

Minimum steps to create a testable Android project (run from repository root):

1. Install dependencies

```bash
pnpm i
pnpm install -D @capacitor/cli @capacitor/core
```

2. Build web assets and initialize Capacitor (one-time)

```bash
pnpm run mobile:init
pnpm run mobile:add-android
```

3. Build the web app and copy to the native project

```bash
pnpm run mobile:prepare
```

4. Open Android Studio

```bash
pnpm run mobile:open
```

Then in Android Studio: build and run on device or emulator. You can also drag the generated `android/` folder into Android Studio.

Notes and caveats
- The extension-specific code in `src/extension/*` (content scripts, manifest, background/service worker) is not used on mobile. Guard or remove imports that only exist for the browser extension.
- dApp connector using content-script injection must be replaced by WalletConnect or an equivalent mobile pairing flow. See `src/extension/connector/rpc/protocol.ts` for the existing protocol.
- Ledger WebUSB/WebHID transports do not work on Android WebView. Use BLE (`@ledgerhq/hw-transport-ble`) or native SDKs with a Capacitor plugin.
- Capacitor uses the `dist` folder as the app `webDir`. Use the existing `pnpm run build:mainnet:chrome` script (invoked by `mobile:build`).

If you want, I can scaffold a small `mobile/` helper script or guard the extension-only imports automatically—tell me if you want that next.
