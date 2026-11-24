---
description: >-
  Faceplugin vs 3DiVi Face SDK. On-premise face recognition, platforms,
  liveness, and ID document products. Public 3DiVi docs versus Faceplugin
  products in these docs.
icon: scale-balanced
---

# vs 3DiVi

[3DiVi Face SDK](https://3divi.ai/products/software/face-sdk) is a C++ face recognition library with wrappers (C#, Java, Python, Swift, Flutter) for Windows, Linux, Android, and iOS. It documents on-prem / on-device / own-cloud deployment and a **2d\_liveness** estimator. 3DiVi also sells OMNI Platform (client-server video) and Image API (REST).

Faceplugin is an **on-premise SDK suite**: Face Recognition, standalone Face Liveness (passive anti-spoofing HTTP + mobile), ID Document Recognition (passport OCR / MRZ), and ID Document Liveness. **Server SDKs** are Docker Hub images licensed with a **machine code** (machine code).

| Criterion                                 | Faceplugin                                                              | 3DiVi                                                      |
| ----------------------------------------- | ----------------------------------------------------------------------- | ---------------------------------------------------------- |
| On-premise / offline after license        | Yes                                                                     | Yes (offline and online license modes documented)          |
| Android / iOS face SDK                    | Yes                                                                     | Yes                                                        |
| Flutter face                              | Yes (Face Recognition + Document)                                       | Yes (Face SDK docs list Flutter)                           |
| React Native / Ionic                      | Yes                                                                     | Check vendor                                               |
| Windows / Linux face                      | Yes (HTTP API + native)                                                 | Yes (C++/Python/C#)                                        |
| Linux Docker Hub HTTP API                 | Yes (`faceplugin/face-recognition`, `face-liveness`, `document-reader`) | Check vendor (local license server mentioned for Docker)   |
| Standalone face anti-spoofing product     | Yes (passive `POST /api/liveness`)                                      | Liveness as Face SDK estimator; BAF / other products exist |
| Passport OCR / ID card SDK                | Yes                                                                     | Check vendor (not the core Face SDK page)                  |
| Document liveness                         | Yes (Linux 8086)                                                        | Check vendor                                               |
| NIST FRVT (as claimed in Faceplugin docs) | Evaluated matching                                                      | Check vendor                                               |
| iBeta / PAD certification                 | No iBeta certification claimed in these docs — ask Faceplugin           | Check vendor                                               |
| License model                             | Offline license key / machine code                                      | Hardware, app id, USB token, online validation options     |

**Choose Faceplugin** when you want a **documented HTTP Docker API** plus **ID OCR and document liveness** in the same vendor docs, with mobile 1:N Identify demos.

**Look at 3DiVi** when you need their C++ Face SDK / OMNI video platform specifically. Confirm Flutter / React Native and document OCR on [3DiVi docs](https://docs.3divi.ai/face_sdk/).

Integrate Faceplugin: [Android Face Recognition](../../products/face-recognition-sdk/recognition-and-liveness/mobile-sdk/face-recognition-android-sdk.md) · [Liveness Linux](../../products/face-recognition-sdk/liveness-detection-sdk/server-sdk/liveness-detection-linux-sdk.md) · [Document Linux](../../products/id-document-recognition-sdk/recognition-and-liveness/server-sdk/id-document-recognition-linux-sdk.md)

### Related documentation

* [SDK comparison index](./) · [Choose a product](../../getting-started/choose-a-product.md) · [Glossary](../resources/glossary.md)
* [FAQ](../resources/faq.md) · [Try it](../../getting-started/try-it.md)
