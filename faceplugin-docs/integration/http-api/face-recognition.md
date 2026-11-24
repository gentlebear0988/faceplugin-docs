---
description: >-
  Faceplugin Face Recognition HTTP API. POST /api/detect, /api/quality,
  /api/feature, /api/match, and /api/similarity on port 8083. JSON or form-data.
  CPU only.
icon: face-viewfinder
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

# Face Recognition

This is the **still-image** Face Recognition API. It runs on Linux Docker image `faceplugin/face-recognition` and on the Windows app. Default port **8083**. The engine is **CPU only**.

Shared routes (health, machine code, activate, license status): [Shared endpoints](shared.md).

Process POSTs return **engine JSON**. There is **no** `POST /api/identify` on the server. There is no server-side 1:N gallery. Store templates in **your** database. Compare them with `/api/similarity`.

A call without a recognition license returns `code` 8 with `licenseError`.

**Face Recognition + Liveness** packages expose the same Face Recognition routes **plus** [`POST /api/liveness`](face-liveness.md) on port **8083**.

Install and run: [Face Recognition Linux SDK](../../products/face-recognition-sdk/recognition/server-sdk/face-recognition-linux-sdk.md) · [Face Recognition Windows SDK](../../products/face-recognition-sdk/recognition/server-sdk/face-recognition-windows-sdk.md).

### Try it

```bash
curl -s http://127.0.0.1:8083/api/health
IMG=$(base64 -w0 face.jpg)
curl -s -X POST http://127.0.0.1:8083/api/detect \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$IMG\"}"
```

More one-liners: [Try it](../../getting-started/try-it.md).

### `POST /api/detect`

Detect faces in one image.

{% tabs %}
{% tab title="JSON" %}
```json
{ "image": "<BASE64>", "cropImage": false }
```

`crop_image` is accepted as an alias.
{% endtab %}

{% tab title="form-data" %}
File field `image` (alias `file`). Text field `cropImage` (`true` / `false`). Same URL.
{% endtab %}
{% endtabs %}

Python: `sdk.detect(image, crop_image=False)`.

|                  |                                  |
| ---------------- | -------------------------------- |
| **Input**        | One image. Optional `cropImage`. |
| **Return value** | Engine JSON (not the envelope).  |

### `POST /api/quality`

Run ICAO-style quality checks on a face image. Same request body as detect. Python: `sdk.quality`.

|                  |                                |
| ---------------- | ------------------------------ |
| **Input**        | One image. Optional crop flag. |
| **Return value** | Engine JSON.                   |

### `POST /api/feature`

Extract a face **template** (feature vector). Store it. Compare it later with `/api/similarity`.

{% tabs %}
{% tab title="JSON" %}
```json
{ "image": "<BASE64>" }
```
{% endtab %}

{% tab title="form-data" %}
File field `image` (alias `file`).
{% endtab %}
{% endtabs %}

Python: `sdk.feature`.

|                  |                                |
| ---------------- | ------------------------------ |
| **Input**        | One image.                     |
| **Return value** | Engine JSON with the template. |

### `POST /api/match`

Compare **two photos** (1:1). The server detects and extracts internally.

{% tabs %}
{% tab title="JSON" %}
```json
{ "image1": "<BASE64>", "image2": "<BASE64>", "cropImage": false }
```
{% endtab %}

{% tab title="form-data" %}
File fields `image1` and `image2`. Text field `cropImage`.
{% endtab %}
{% endtabs %}

Python: `sdk.match`.

|                  |                                      |
| ---------------- | ------------------------------------ |
| **Input**        | Two images. Optional crop flag.      |
| **Return value** | Engine JSON with a similarity score. |

### `POST /api/similarity`

Compare **two templates** you already extracted.

```json
{ "feature1": "<BASE64>", "feature2": "<BASE64>" }
```

Aliases: `template1` / `template2`. Missing features return envelope `code: -1`. Both features must decode to the **same length**. Form-data uses **text** fields (not files).

### Related documentation

* [HTTP API overview](./) · [Face Liveness HTTP API](face-liveness.md) · [Hosting requirements](../deploy-and-host/hosting-requirements.md)
