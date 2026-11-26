---
description: >-
  Faceplugin SDK FAQ. Passport OCR, ID verification, Face Recognition API,
  passive vs active liveness, offline licensing, Flutter, Docker machine codes,
  and thresholds.
icon: circle-question
---

# FAQ

### What can Faceplugin ID Document Recognition (Document Reader) do?

On-premise **passport OCR**, **MRZ reading**, ID card and driver license verification, barcode/QR, document type classification (**16,900** templates / **255** countries), and optional document authenticity. See [Document capabilities](../../products/id-document-recognition-sdk/capabilities.md) and [Passport OCR & ID verification](passport-ocr-and-id-verification.md).

### Is Faceplugin a passport OCR SDK / MRZ reader?

Yes. The ID Document Recognition SDK reads passports and MRZ (when the template includes one), plus national IDs and licenses. HTTP API on port **8082**.

### What is a face recognition SDK?

A library that detects faces, extracts a template (embedding), and compares templates. Faceplugin’s commercial SDK runs **on-premise**. You store templates in **your** database. See [Face SDK capabilities](../../products/face-recognition-sdk/capabilities.md).

### Is there a Face Recognition API?

Yes. Linux Docker and Windows expose an HTTP API on port **8092** (`/api/detect`, `/api/match`, `/api/similarity`). The separate Face Liveness service exposes `/api/liveness` on its own port, **8093**. Full reference: [Face Recognition HTTP API](../../integration/http-api/face-recognition.md). Overview: [HTTP API](../../integration/http-api/).

### What is passive liveness detection?

The engine scores a camera frame or JPEG **without** a smile / turn-head challenge. It is the only mode the Face Liveness server SDK offers, and the default mode on mobile. These docs do **not** claim iBeta certification. Contact Faceplugin if you need a certification statement. See [Face SDK capabilities](../../products/face-recognition-sdk/capabilities.md).

### What is the difference between active and passive liveness?

**Passive:** no user challenge—the engine scores a single frame. Used on the server and in Face Recognition’s Identify mode. **Active:** part of the Face Liveness **mobile** SDK. The face is scored for spoofing first, as an anti-spoofing gate; only a face that clears the gate is asked for a short shuffled sequence of challenges (blink, smile, turn left/right, look up/down, move closer), and the result reports the gate score plus every challenge outcome. Face Liveness on Linux/Windows stays passive: `POST /api/liveness` on one JPEG.

### Face Recognition liveness vs Face Liveness SDK?

Identify on mobile includes passive **2D liveness** as part of matching. Standalone Face SDK **Liveness** is **anti-spoofing only** (no enroll / 1:N), and on mobile it adds an active challenge mode. See [Face SDK capabilities](../../products/face-recognition-sdk/capabilities.md).

### Can face recognition work completely offline?

Yes. After you activate with a license key bound to the app id (mobile) or machine code (server), matching does not need the internet.

### Can Faceplugin run on-premise?

Yes. That is the product model: phone, Windows/Linux host, or Docker on **your** infrastructure.

### Does the Faceplugin SDK require an internet connection?

Not for inference after activation. You need a network only to clone GitHub, pull Docker Hub, download Google Drive runtimes, and to contact Faceplugin for a license.

### Does Faceplugin support Flutter?

Yes for Face Recognition, Face Liveness and ID Document Recognition (`face_recognition_sdk`, `face_liveness_sdk`, `document_reader_sdk`). Face Liveness also ships React Native (`face-liveness-sdk`), Ionic Capacitor (`face-liveness-capacitor`), Ionic Cordova (`face-liveness-cordova`) and .NET MAUI, beside Android and iOS.

### Does Faceplugin support iOS?

Yes for Face Recognition, Face Liveness, and ID Document Recognition. Physical iPhone; demo bundle ids are listed on each iOS page.

### How many ID document types are supported?

**16,900** templates across **255** countries and territories. Download the [Supported documents PDF](../../products/id-document-recognition-sdk/capabilities.md#document-type-classification--worldwide-coverage).

### Docker vs host machine code

`GET /api/machinecode` returns machine code. Docker and a native host on the same machine produce **different** codes. License the environment you run in production. See [Request a License](../request-a-license-and-support.md).

### Demo license key and my own app id

Sample keys are bound to the **demo** application id / bundle id on the platform page. Request a new key for your production id.

### Match and liveness thresholds

Mobile Identify default match **0.67**. Face Liveness server: score **≥ 0.5** → Real / pass; that verdict threshold is hard-coded in the native engine and is not configurable over HTTP, so apply your own cut-off to the returned `score` if you need a stricter one. Change the mobile defaults only if Faceplugin support gives you different recommended thresholds for your license.

### How large is the Android SDK?

The GitHub repo is small. Native models (AAR + `.fpk` / engine) come from Google Drive and are large — that is why they are not on GitHub. Check the Google Drive folder size for the product you licensed.

### Is there a Node.js or C++ SDK?

Document Reader has public Node / Go / C++ HTTP options. Face server integration is primarily HTTP (`curl` from any language) or Python `sdk.py` on the same host as `lib/cpu/`. See [Choose a product](../../getting-started/choose-a-product.md).

### Where are acronyms defined?

See the [Glossary](glossary.md) for MRZ, OCR, eKYC, 1:1 / 1:N, PAD, FRVT, license key, and machine code.

### Related documentation

* [HTTP API](../../integration/http-api/) · [Deploy and host](../../integration/deploy-and-host/)
* [Glossary](glossary.md) · [Try it](../../getting-started/try-it.md) · [Troubleshooting](troubleshooting.md) · [Status codes](status-codes.md)
* [Choose a product](../../getting-started/choose-a-product.md) · [Passport OCR & ID verification](passport-ocr-and-id-verification.md)
* [ID Document capabilities](../../products/id-document-recognition-sdk/capabilities.md) · [Face SDK capabilities](../../products/face-recognition-sdk/capabilities.md)
* [SDK comparison](../comparisons/)
