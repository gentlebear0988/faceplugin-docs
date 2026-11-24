---
description: >-
  Faceplugin ID Document Liveness Server SDK. On-premise document anti-spoofing
  HTTP API for Linux Docker on port 8086. POST /api/documentLiveness. No OCR.
icon: server
---

# Server

Document authenticity only (screen replay, print, substitution). Optical character recognition (OCR), MRZ, and barcodes are **off**.

Typical call order: `GET /api/machinecode` → send machine code → `POST /api/activate` → `POST /api/documentLiveness`. Default port **8086**.

For **fields plus authenticity** in one engine, use [ID Document Recognition Server SDK](../../recognition-and-liveness/server-sdk/) with a Liveness-capable license.

There is no public mobile SDK for this product.

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td><strong>Linux SDK</strong></td><td>Docker image faceplugin/document-liveness. HTTP API on port 8086.</td><td><a href="../../../../.gitbook/assets/linux-docker.png">linux-docker.png</a></td><td><a href="id-document-liveness-linux-sdk.md">id-document-liveness-linux-sdk.md</a></td></tr></tbody></table>

```mermaid
flowchart LR
  PageImage --> Authenticity
  Authenticity --> SecurityJSON
```

### Frequently Asked Questions

**What is the ID document liveness API?**

`POST /api/documentLiveness` on port 8086, called after `POST /api/activate`. It checks whether a document image shows a genuine physical ID.

**Does the document liveness server read the MRZ or do OCR?**

No. OCR, MRZ and barcode reading are off. Use ID Document Recognition to read document fields.

**What attacks does it detect?**

Screen replays, printed copies and substitution.

**Which platforms are supported?**

Linux with Docker, using the `faceplugin/document-liveness` image. There is no public mobile SDK.

**Can I get OCR and authenticity checks from one server?**

Yes. Use the ID Document Recognition Server SDK with a liveness-capable license.

### Related documentation

* [ID Document Liveness SDK](../) · [Linux SDK](id-document-liveness-linux-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [ID Document Recognition Server SDK](../../recognition-and-liveness/server-sdk/)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
