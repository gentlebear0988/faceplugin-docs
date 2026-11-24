---
description: >-
  Faceplugin ID Document Liveness SDK. On-premise document anti-spoofing HTTP
  API for Linux and Docker. Screen, print, and substitution attacks. No OCR.
  Port 8086.
icon: stamp
---

# Liveness

### Overview

**Faceplugin ID Document Liveness SDK** checks whether the image is a **physical ID** or an attack: a screen replay, a printout, a digitally created page, or a substituted portrait.

This product does **not** read fields. OCR, MRZ (machine-readable zone), and barcodes are turned off. For **passport OCR plus authenticity** in one engine, use [ID Document Recognition](../) with a Liveness-capable license. This is **not** face anti-spoofing — use [Face Liveness Detection](../../face-recognition-sdk/liveness-detection-sdk/) for selfies.

Today the public **SDK** is **Linux / Docker** (HTTP port **8086**). There is no public mobile SDK.

```mermaid
flowchart LR
  PageImage --> Authenticity
  Authenticity --> SecurityJSON
```

### Features

✅ Document authenticity and liveness checks

✅ Screen replay and digital-source detection

✅ Printed copy checks

✅ Front and back (multi-page) images

✅ On-premise (your machine only)

### Platforms

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td><strong>Server SDK</strong></td><td>Linux / Docker authenticity-only HTTP API on port 8086.</td><td><a href="server-sdk/">server-sdk</a></td></tr></tbody></table>

### Use Cases

* **KYC authenticity gate:** reject screen replays and printouts before you run OCR (`POST /api/documentLiveness`, port 8086).
* **Split architecture:** run authenticity checks on Linux while OCR runs in a separate service.
* **Fraud prevention:** confirm a physical ID is present without reading any personal fields.

### Frequently Asked Questions

**What is ID document liveness detection?**

It checks that a document image shows a real, physical ID rather than a screen replay, a printout, a digitally created page or a substituted portrait.

**Does the ID Document Liveness SDK read the MRZ or do OCR?**

No. OCR, MRZ and barcode reading are turned off. Use ID Document Recognition for OCR and MRZ.

**How do I call the document liveness API?**

Call `POST /api/documentLiveness` on port 8086 after activating with `POST /api/activate`. See [Linux SDK](server-sdk/id-document-liveness-linux-sdk.md).

**Is this the same as face liveness detection?**

No. This product checks ID documents. For selfie anti-spoofing, use Face Liveness Detection.

**Is there a mobile SDK for document liveness?**

Not publicly today. The SDK is available for Linux / Docker.

**Can I get OCR and authenticity checks together?**

Yes. Use ID Document Recognition with a liveness-capable license to get both from one engine.

### Related documentation

* [ID Document SDK](../) · [Face SDK](../../face-recognition-sdk/) · [Glossary](../../../help/resources/glossary.md)
* [Document security check fields](../document-security-check-fields.md)
* [Request a License](../../../help/request-a-license-and-support.md) · [Try it](../../../getting-started/try-it.md)
* [Combining products (eKYC)](../../../getting-started/choose-a-product.md) · [SDK comparison](../../../help/comparisons/)
