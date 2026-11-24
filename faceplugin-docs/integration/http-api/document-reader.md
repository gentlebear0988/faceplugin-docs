---
description: >-
  Faceplugin ID Document Recognition HTTP API. POST /api/documentRecognition,
  /api/documentLiveness, /api/documentProcess, and /api/generalProcess on port
  8082.
icon: file-lines
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

# ID Document Recognition

This is the HTTP API for **ID Document Recognition**. It runs on Linux Docker image `faceplugin/document-reader` and on the Windows app. Default port **8082**.

| Route                 | Use it for                                            |
| --------------------- | ----------------------------------------------------- |
| `documentRecognition` | OCR, MRZ, barcode, image quality only                 |
| `documentLiveness`    | Authenticity only                                     |
| `documentProcess`     | Combined pipeline (fields plus optional authenticity) |

**MRZ** is the machine-readable zone on a passport. **OCR** means reading printed text from the image.

Shared routes (including `GET /api/licenseStatus`): [Shared endpoints](shared.md).

Install and run: [ID Document Recognition Linux SDK](../../products/id-document-recognition-sdk/recognition-and-liveness/server-sdk/id-document-recognition-linux-sdk.md) · [ID Document Recognition Windows SDK](../../products/id-document-recognition-sdk/recognition-and-liveness/server-sdk/id-document-recognition-windows-sdk.md).

Result fields: [Document result JSON](../../products/id-document-recognition-sdk/document-result-json.md). Security keys: [Document security check fields](../../products/id-document-recognition-sdk/document-security-check-fields.md).

### Try it

```bash
curl -s -X POST http://127.0.0.1:8082/api/documentRecognition \
  -H 'Content-Type: application/json' \
  -d "{\"images\":[{\"image\":\"$(base64 -w0 id.jpg)\"}]}"
```

### `POST /api/documentRecognition`

OCR / MRZ / barcode / image quality only. Authenticity is **always off**. Caller `response` flags are ignored.

{% tabs %}
{% tab title="JSON" %}
```json
{
  "images": [
    { "image": "<BASE64>", "page_idx": 0 },
    { "image": "<BASE64>", "page_idx": 1 }
  ]
}
```
{% endtab %}

{% tab title="form-data" %}
Repeated file field `images` (or a single `image` / `file`). Optional file `rfid`. Text `isCropped`.
{% endtab %}
{% endtabs %}

|                  |                                                                                          |
| ---------------- | ---------------------------------------------------------------------------------------- |
| **Input**        | One or more page images (base64). Same `images` shape as `documentProcess`.              |
| **Return value** | Engine JSON with OCR / MRZ / barcode / `imageQuality`. No `security` authenticity block. |

Python:

```python
import sdk
sdk.activate("license.txt")
sdk.init_sdk()
result = sdk.document_recognition(
    [{"image": base64_front}, {"image": base64_back, "page_idx": 1}],
)
```

### `POST /api/documentLiveness`

Authenticity / security only. OCR / MRZ / barcode / image quality are **always off**. Caller `response` flags are ignored.

Use the same image body as `documentRecognition`. The dedicated Document Liveness product on port **8086** uses this same path. On Document Reader (**8082**) it is authenticity-only against the recognition engine.

|                  |                                                                                           |
| ---------------- | ----------------------------------------------------------------------------------------- |
| **Input**        | One or more page images (base64).                                                         |
| **Return value** | Engine JSON with `security` checks when the license includes Liveness. No `images` crops. |

Python: `sdk.document_liveness(images)`.

Also see [ID Document Liveness HTTP API](document-liveness.md).

### `POST /api/documentProcess`

Run a full document process (fields plus optional security).

{% tabs %}
{% tab title="JSON" %}
```json
{
  "images": [
    { "image": "<BASE64>", "page_idx": 0 },
    { "image": "<BASE64>", "page_idx": 1 }
  ],
  "rfid": "",
  "response": {
    "OCR": "normal",
    "MRZ": "normal",
    "Barcode": "normal",
    "ImageQuality": "normal",
    "Authenticity": "normal"
  }
}
```
{% endtab %}

{% tab title="form-data" %}
Repeated file field `images` (or a single `image` / `file`). Optional file `rfid`. Text `options` or `response` as a JSON string. Text `isCropped`.
{% endtab %}
{% endtabs %}

* `images` may also be a list of raw base64 strings. A single `"image"` field becomes a one-page list.
* You may nest flags as `"options": { "response": {...}, "isCropped": ... }`. If you omit `options`, top-level `response` / `isCropped` are used.
* `"Authenticity": "none"` skips liveness checks. `"normal"` runs them when the license includes Liveness. Windows also documents `"strict"`.
* `"ImageQuality": "none"` skips capture-quality checks and omits `imageQuality` from the result. `"normal"` (default) runs them.
* Optional `rfid` is a string passed through to the engine. The Gradio demo does not walk through NFC/RFID capture.

|                  |                                                      |
| ---------------- | ---------------------------------------------------- |
| **Input**        | One or more page images (base64) and response flags. |
| **Return value** | Engine JSON (not the envelope).                      |

Python:

```python
import sdk
sdk.activate("license.txt")
sdk.init_sdk()
result = sdk.document_process(
    [{"image": base64_front}, {"image": base64_back, "page_idx": 1}],
    rfid="",
    options={"response": {"OCR": "normal", "MRZ": "normal", "Barcode": "normal", "ImageQuality": "normal", "Authenticity": "normal"}},
)
```

Session helpers on `sdk.py` (`start_new_session()`, `start_new_page()`, `unload()`, `get_license_status()`) are **not** separate HTTP routes.

### `POST /api/generalProcess`

Single-image general process.

```json
{ "image": "<BASE64>", "options": {} }
```

|                  |                                         |
| ---------------- | --------------------------------------- |
| **Input**        | One base64 image.                       |
| **Return value** | Engine JSON from `sdk.general_process`. |

### Related documentation

* [HTTP API overview](./) · [Document result JSON](../../products/id-document-recognition-sdk/document-result-json.md) · [Hosting requirements](../deploy-and-host/hosting-requirements.md)
