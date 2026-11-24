---
description: >-
  Faceplugin ID Document Liveness HTTP API. POST /api/documentLiveness on port
  8086. Authenticity only — OCR, MRZ, and barcode are off.
icon: stamp
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

# ID Document Liveness

This API runs **document authenticity** (anti-spoofing) only. It does not return OCR fields, MRZ lines, or barcodes.

It runs on Docker image `faceplugin/document-liveness` on port **8086**.

Shared routes (including `GET /api/licenseStatus`): [Shared endpoints](shared.md).

If you need **fields and** authenticity together, call [ID Document Recognition HTTP API](document-reader.md) `documentProcess` with a Liveness-capable license. Document Reader on port **8082** also exposes `POST /api/documentLiveness` as authenticity-only.

Install and run: [ID Document Liveness Linux SDK](../../products/id-document-recognition-sdk/id-document-liveness-sdk/server-sdk/id-document-liveness-linux-sdk.md).

### Try it

```bash
curl -s -X POST http://127.0.0.1:8086/api/documentLiveness \
  -H 'Content-Type: application/json' \
  -d "{\"images\":[{\"image\":\"$(base64 -w0 id.jpg)\"}]}"
```

### `POST /api/documentLiveness`

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
Repeated file field `images` (or a single `image` / `file`). Same URL.
{% endtab %}
{% endtabs %}

A lone `"image"` field becomes a one-page list. Optional `options` or top-level `response` are forwarded. The product always requests authenticity. OCR / MRZ / barcode stay off.

|                  |                                                                 |
| ---------------- | --------------------------------------------------------------- |
| **Input**        | One or more page images (base64). Front and back are supported. |
| **Return value** | Engine JSON from `sdk.document_liveness`.                       |

```python
import sdk
sdk.activate("license.txt")
sdk.init_sdk()
print(sdk.document_liveness([{"image": base64_front}]))
```

Security field names: [Document security check fields](../../products/id-document-recognition-sdk/document-security-check-fields.md).

### Related documentation

* [HTTP API overview](./) · [ID Document Recognition HTTP API](document-reader.md) · [Hosting requirements](../deploy-and-host/hosting-requirements.md)
