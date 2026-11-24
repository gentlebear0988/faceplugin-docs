---
description: >-
  Troubleshoot Faceplugin SDK demos. Missing AAR or frameworks, wrong app id
  licenses, Docker shm-size, Yarn 3 on React Native, and cameras that stay black
  on emulators.
icon: screwdriver-wrench
---

# Troubleshooting

Start here when a Faceplugin demo never reaches **Ready**, the camera stays black, or HTTP process calls fail after Docker starts.

### Home never shows Ready / tiles stay locked

1. Native runtime missing or nested in a subfolder. Check the exact AAR / framework path in that SDK's platform guide.
2. Wrong application id — demo license key only matches the sample id.
3. `init` returned 1–4. See [Status codes](status-codes.md).
4. Called activate/init on the UI thread (Android) or before the binary was packaged (hot reload is not enough).

### Camera black / crash

Physical device, `CAMERA` permission / `NSCameraUsageDescription`, arm64. Document Reader and Face plugins often filter `arm64-v8a` only in the demo.

### Docker starts then process calls fail

Activate with `POST /api/activate`. Detached Compose does not prompt. `--shm-size` too small for Document Reader (`dcr.fpk`). Privileged + machine-id volume as in the README. Sizing and Docker flags: [Hosting requirements](../../integration/deploy-and-host/hosting-requirements.md). Endpoint contracts: [HTTP API](../../integration/http-api/).

### `lib/cpu` “file not found”

You unzipped into `lib/cpu/FolderName/`. Files must sit **directly** in `cpu/`.

### React Native `npm install` broke the repo

Use **Yarn 3** as `packageManager`. npm workspaces are not supported at the root.

### Ionic / Flutter in the browser

`ionic serve` and Flutter web do not load the engine. Open Android Studio / Xcode after `cap sync` / `flutter run`.

### Two products in one APK / one `lib/`

Do not dump a second product’s Google Drive runtime into another product’s `lib/cpu/`. Run separate servers instead. See [Architecture](../../integration/deploy-and-host/architecture.md).

### Still stuck

Email [info@faceplugin.com](mailto:info@faceplugin.com) with: OS, GitHub repo name, activate/init code, and whether you used Docker or Google Drive.

### Related documentation

* [HTTP API](../../integration/http-api/) · [Hosting requirements](../../integration/deploy-and-host/hosting-requirements.md) · [Architecture](../../integration/deploy-and-host/architecture.md)
* [FAQ](faq.md) · [Glossary](glossary.md) · [Status codes](status-codes.md) · [Try it](../../getting-started/try-it.md)
* [Request a License](../request-a-license-and-support.md)
