---
description: >-
  Faceplugin document verification API for Windows and Linux Docker. On-premise
  passport OCR, MRZ, and authenticity HTTP on port 8082. documentRecognition and
  documentProcess.
icon: server
---

# Server

ID Document Recognition as an HTTP API on **your** machine. Send page images; receive OCR, MRZ, barcode, image quality, and optional authenticity JSON.

Typical call order: `GET /api/machinecode` → send machine code → `POST /api/activate` → `POST /api/documentRecognition` / `documentLiveness` / `documentProcess`. Default port **8082**.

Parse results with [Document result JSON](../../document-result-json.md). Authenticity field names: [Document security check fields](../../document-security-check-fields.md).

Authenticity **without** OCR is a separate product: [ID Document Liveness SDK](../../id-document-liveness-sdk/).

For on-device capture, use [Mobile SDK](../mobile-sdk/).

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td><strong>Linux SDK</strong></td><td>Docker image faceplugin/document-reader. Same HTTP API as Windows.</td><td><a href="../../../../.gitbook/assets/linux-docker.png">linux-docker.png</a></td><td><a href="id-document-recognition-linux-sdk.md">id-document-recognition-linux-sdk.md</a></td></tr><tr><td></td><td><strong>Windows SDK</strong></td><td>HTTP API on port 8082. No Docker on Windows.</td><td><a href="../../../../.gitbook/assets/windows.png">windows.png</a></td><td><a href="id-document-recognition-windows-sdk.md">id-document-recognition-windows-sdk.md</a></td></tr><tr><td></td><td><strong>Node SDK</strong></td><td>HTTP API on port 8082. Optional native libDocSDK.</td><td><a href="../../../../.gitbook/assets/javascript.png">javascript.png</a></td><td><a href="id-document-recognition-node-sdk.md">id-document-recognition-node-sdk.md</a></td></tr></tbody></table>

```mermaid
flowchart LR
  PageImage --> documentProcess
  documentProcess --> OCR_MRZ
  OCR_MRZ --> Authenticity
  Authenticity --> JSON
```

### Frequently Asked Questions

**Is this a document verification API?**

Yes. On port 8082 it offers `POST /api/documentRecognition`, `/api/documentLiveness` and `/api/documentProcess`.

**Can I get passport OCR without authenticity checks?**

Yes. Use `documentRecognition`. Authenticity-only checks are a separate product, [ID Document Liveness](../../id-document-liveness-sdk/) on port **8086**.

**Which platforms does the server SDK support?**

Linux with Docker (`faceplugin/document-reader`), Windows without Docker, and Node.js.

**Does the API send document images to the cloud?**

No. It runs as an HTTP API on your own machine.

**What does the API return?**

JSON with OCR, MRZ, barcode, image quality and optional authenticity results.

### Related documentation

* [Capabilities](../../capabilities.md) · [Faceplugin ID Document Recognition Mobile SDK](../mobile-sdk/) · [ID Document Recognition SDK](../../) · [Glossary](../../../../help/resources/glossary.md)
* [ID Document Liveness Server SDK](../../id-document-liveness-sdk/server-sdk/)
* [Document result JSON](../../document-result-json.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
