---
description: >-
  Faceplugin HTTP API for Linux, Docker, and Windows. Ports, response shapes,
  and links to per-product endpoint pages.
icon: brackets-curly
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
  actions:
    visible: true
  anchors:
    visible: true
---

# HTTP API

Use this section when you run Faceplugin on **Linux, Docker, or Windows**. Those apps expose an HTTP API.

Mobile SDKs use the same ideas (activate, process, JSON results). They do **not** use these URLs. Call the native or plugin methods on each mobile platform page instead.

### Base URL

```
http://{host}:{port}
```

Paths do **not** include `/v1`.

| Product                 | Default port | What it does                             |
| ----------------------- | -----------: | ---------------------------------------- |
| ID Document Recognition |         8082 | OCR, MRZ, barcode, optional authenticity |
| Face Recognition        |         8092 | Detect, quality, template, 1:1 match, 1:N identify |
| Face Liveness           |         8084 | Score one face image for spoofing        |
| ID Document Liveness    |         8086 | Document authenticity only               |

**MRZ** means the machine-readable zone on a passport. **OCR** means reading printed text from the image.

These Flask apps allow CORS from any origin (`*`). Do not treat CORS as access control. See [Production deployment](../deploy-and-host/production-deployment.md).

### Two response shapes

This is the most common integration mistake. The apps use **two** shapes.

#### Envelope (shared routes)

Some routes wrap the result in a small JSON object. We call that an **envelope**.

These routes use an envelope: `GET /api/health`, `/api/machinecode`, `/api/backend`, `/api/licenseStatus`, and `POST /api/activate`.

```json
{
  "success": true,
  "code": 0,
  "message": "OK",
  "request_id": null,
  "data": {}
}
```

#### Engine JSON (process routes)

Process routes do **not** use an envelope. They return the engine JSON as the HTTP body.

Examples: `POST /api/detect`, `/api/liveness`, `/api/documentProcess`, `/api/documentRecognition`, `/api/documentLiveness`.

Parse that JSON directly. Do not look for a `success` or `data` wrapper.

{% hint style="info" %}
`POST /api/activate` accepts raw `FP1.…` text, JSON `{"license":"FP1.…"}`, or a license file body. An empty body returns envelope `code: -1`.
{% endhint %}

### Images

Send a **base64** image (usually a JPEG) in JSON. Or upload the same field names as **files** with `multipart/form-data`. The path stays the same. The response JSON is the same.

For template compare (`/api/similarity`), send templates as **text** fields (`feature1` / `feature2`). Do not send them as files.

A **template** is a face feature vector you can store and compare later.

### What is not in these apps

| Name you might expect           | Shipping apps                                             |
| ------------------------------- | --------------------------------------------------------- |
| `POST /api/identify`            | **Not** in Face Recognition Linux / Windows               |
| `POST /api/documentDetect`      | **Not** in Document Reader Linux / Windows                |
| `POST /api/documentRecognize`   | **Not** — use `documentRecognition` or `documentProcess`  |
| `POST /api/verify` / `/api/idv` | **No** all-in-one Face Verification or IDV HTTP app       |
| GPU tags / `lib/gpu/`           | **Not** in current Face Recognition or Face Liveness apps |

### Postman

Import `postman/<Product>-API.postman_collection.json` from the repository you cloned.

### Per-product pages

* [Shared endpoints](shared.md) — health, machine code, activate
* [Face Recognition HTTP API](face-recognition.md)
* [Face Liveness HTTP API](face-liveness.md)
* [ID Document Recognition HTTP API](document-reader.md)
* [ID Document Liveness HTTP API](document-liveness.md)

### Related documentation

* [Try it](../../getting-started/try-it.md) · [Deploy and host](../deploy-and-host/) · [Status codes](../../help/resources/status-codes.md)
