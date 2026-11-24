---
description: >-
  Faceplugin ID Document Recognition Node.js HTTP API. Same documentProcess
  routes as Python on port 8082. Optional native libDocSDK; stub without it.
icon: node-js
---

# Node.js

Node.js **HTTP API** with the same routes as the Python Document Reader server (`POST /api/documentProcess`, license routes, …). Default port **8082**. No Docker.

Native `libDocSDK` is optional. Without it the server can run a demo stub (`DOCSDK_STUB=1`) that mimics the Gradio UI shape. For production OCR, use the Python Linux/Windows HTTP SDK or provide the native library.

### Code

{% embed url="https://github.com/Faceplugin-ltd/ID-Document-Recognition-Node" %}

### Setup

Sizing: [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md).

```bash
npm start
```

Web demos (React / Vue / Angular / JavaScript) can point at this process on **8082**.

Also public: [Go](https://github.com/Faceplugin-ltd/ID-Document-Recognition-Go) and [C/C++](https://github.com/Faceplugin-ltd/ID-Document-Recognition-CPP) HTTP APIs with the same route family.

### APIs

| Endpoint                        | Purpose                                  |
| ------------------------------- | ---------------------------------------- |
| `GET /api/health`               | Process is listening (no license)        |
| `GET /api/machinecode`          | Machine code                             |
| `GET /api/licenseStatus`        | License status                           |
| `POST /api/activate`            | Activate                                 |
| `POST /api/documentRecognition` | OCR / MRZ / barcode / image quality only |
| `POST /api/documentLiveness`    | Authenticity only                        |
| `POST /api/documentProcess`     | Combined OCR + optional authenticity     |
| `POST /api/generalProcess`      | Single-image general process             |

Full reference: [Document Reader HTTP API](../../../../integration/http-api/document-reader.md). Shared control routes: [Shared endpoints](../../../../integration/http-api/shared.md).

Same route family as the Python Document Reader server. Without native `libDocSDK`, use `DOCSDK_STUB=1` for a demo stub only.

### Related documentation

* [Linux SDK](id-document-recognition-linux-sdk.md) · [Server SDK](./) · [Glossary](../../../../help/resources/glossary.md)
* [Document result JSON](../../document-result-json.md) · [Choose a product](../../../../getting-started/choose-a-product.md)
