---
description: >-
  Faceplugin vs Luxand FaceSDK. On-premise face recognition and liveness versus
  Luxand’s FaceSDK library. Document OCR is Faceplugin-only in this table.
icon: scale-balanced
---

# vs Luxand

[Luxand FaceSDK](https://www.luxand.com/facesdk/) is a long-running **on-premise face library** (Windows, Linux, macOS, iOS, Android, plus language bindings). It documents **passive and active** liveness and thermal-camera liveness. Luxand.cloud is a **separate cloud FaceAPI**. Public reviews describe FaceSDK as face matching/liveness **without** a document-verification product.

Faceplugin adds a **document recognition** SDK (passport OCR, MRZ, barcode) and **document liveness**, plus Linux/Windows **HTTP Docker APIs** and Flutter / React Native / Ionic Face Recognition and Document plugins.

| Criterion                      | Faceplugin                                                                        | Luxand FaceSDK                  |
| ------------------------------ | --------------------------------------------------------------------------------- | ------------------------------- |
| On-premise / offline           | Yes                                                                               | Yes (FaceSDK; FaceAPI is cloud) |
| Android / iOS                  | Yes                                                                               | Yes                             |
| Flutter / React Native / Ionic | Yes (FR + Document)                                                               | Check vendor                    |
| Windows / Linux                | Yes                                                                               | Yes                             |
| macOS SDK                      | Check vendor                                                                      | Yes (FaceSDK site)              |
| Face recognition               | Yes                                                                               | Yes                             |
| Passive liveness               | Yes                                                                               | Yes (documented)                |
| Active liveness                | Public GitHub: Active-Liveness-Detection-Android / iOS. Not the Face Liveness SDK | Yes (Tracker API samples)       |
| Passport OCR / ID card SDK     | Yes                                                                               | No (public FaceSDK positioning) |
| Document liveness              | Yes                                                                               | Check vendor                    |
| HTTP Docker Hub images         | Yes                                                                               | Check vendor                    |
| NIST FRVT                      | Evaluated matching (Faceplugin docs)                                              | Check vendor                    |

**Choose Faceplugin** when you need **ID document OCR + face + anti-spoofing** as documented products, or a **ready HTTP API** on Docker.

**Look at Luxand** when you want a general-purpose FaceSDK (including macOS / thermal) and will add OCR from another vendor.

Integrate Faceplugin: [Android Face Recognition](../../products/face-recognition-sdk/recognition-and-liveness/mobile-sdk/face-recognition-android-sdk.md) · [Liveness Linux](../../products/face-recognition-sdk/liveness-detection-sdk/server-sdk/liveness-detection-linux-sdk.md) · [Document Linux](../../products/id-document-recognition-sdk/recognition-and-liveness/server-sdk/id-document-recognition-linux-sdk.md)

### Related documentation

* [SDK comparison index](./) · [Choose a product](../../getting-started/choose-a-product.md) · [Glossary](../resources/glossary.md)
* [FAQ](../resources/faq.md) · [Try it](../../getting-started/try-it.md)
