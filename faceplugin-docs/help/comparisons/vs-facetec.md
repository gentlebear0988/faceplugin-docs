---
description: >-
  Faceplugin vs FaceTec. 2D on-premise face/liveness/document SDKs versus
  FaceTec 3D FaceMap liveness, server SDK, and usage-based 3D liveness billing.
icon: scale-balanced
---

# vs FaceTec

[FaceTec](https://www.facetec.com/) focuses on **3D liveness** (3D FaceMap / FaceScan), 3D face matching, and ID scan/OCR as part of that flow. Device SDKs target **iOS, Android, and webcams**. Customers run FaceTec **Server SDK** on their servers; FaceTec’s site describes **monthly 3D liveness usage** (minimum commitment) with ID scans included for customers. Certified PAD is a core FaceTec marketing claim.

Faceplugin ships **2D** on-premise engines: still-image or camera-frame matching and **passive** anti-spoofing, plus standalone passport OCR and document liveness. Linux/Windows are **HTTP APIs** you host (ports 8083 / 8084 / 8082). License is an offline license key, not a per-liveness monthly meter in these docs.

| Criterion                      | Faceplugin                                                                 | FaceTec                                                              |
| ------------------------------ | -------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| On-premise processing          | Yes (device or your HTTP host)                                             | Yes (customer-hosted Server SDK; also cloud options)                 |
| Offline after activation       | Yes                                                                        | Check vendor (3D session typically talks to **your** FaceTec server) |
| Android / iOS                  | Yes                                                                        | Yes                                                                  |
| Flutter / React Native / Ionic | Yes (FR + Document; Liveness is Android/iOS)                               | Check vendor                                                         |
| Windows / Linux HTTP API       | Yes                                                                        | Server SDK / REST on **your** FaceTec server                         |
| Passive 2D anti-spoofing       | Yes                                                                        | 3D liveness is the product                                           |
| Active / challenge liveness    | Not the Face Liveness SDK                                                  | 3D video-selfie capture                                              |
| Passport OCR / ID scan         | Yes (Document Recognition SDK)                                             | Yes (Photo ID OCR / barcode / NFC per FaceTec site)                  |
| Document liveness product      | Yes (Linux)                                                                | ID tampering checks in Identity Check flow                           |
| Flutter Face Liveness SDK      | No public SDK                                                              | Check vendor                                                         |
| Pricing model in public docs   | License key / machine code                                                 | Monthly 3D liveness usage + minimums (per FaceTec.com)               |
| NIST / iBeta                   | NIST FRVT evaluated matching; no iBeta certification claimed in these docs | FaceTec publishes PAD certification claims — verify on FaceTec.com   |

**Choose Faceplugin** when you want **unlimited on-prem inference after license**, 2D JPEG/`faceDetection` APIs, Docker Hub images, and a **separate** document OCR engine you combine yourself ([eKYC](../../getting-started/choose-a-product.md#combining-products-ekyc)).

**Look at FaceTec** when you specifically need **3D FaceMap liveness** and their certified PAD / 3D matching stack.

Integrate Faceplugin: [Android Face Recognition](../../products/face-recognition-sdk/recognition-and-liveness/mobile-sdk/face-recognition-android-sdk.md) · [Liveness Linux](../../products/face-recognition-sdk/liveness-detection-sdk/server-sdk/liveness-detection-linux-sdk.md) · [Document Linux](../../products/id-document-recognition-sdk/recognition-and-liveness/server-sdk/id-document-recognition-linux-sdk.md)

### Related documentation

* [SDK comparison index](./) · [Choose a product](../../getting-started/choose-a-product.md) · [Glossary](../resources/glossary.md)
* [FAQ](../resources/faq.md) · [Try it](../../getting-started/try-it.md)
