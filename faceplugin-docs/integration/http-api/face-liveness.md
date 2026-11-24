---
description: >-
  Faceplugin Face Liveness HTTP API. POST /api/liveness (alias
  /api/check_liveness) with a JPEG on port 8084. Score 0.5 or higher is Real.
  CPU only.
icon: user-shield
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

# Face Liveness

This API scores **one RGB JPEG** for presentation attacks. Examples include a printed photo, a screen replay, a mask, or a deepfake-style spoof.

**PAD** means presentation attack detection (anti-spoofing).

It runs on Docker image `faceplugin/face-liveness` and on the Windows app. Default port **8084**. The engine is **CPU only**.

Shared routes: [Shared endpoints](shared.md). A call without a liveness license returns `code` 8 with `licenseError`.

The combined Face Recognition + Liveness packages also expose `/api/liveness` on port **8083**. See [Face Recognition HTTP API](face-recognition.md).

Install and run: [Face Liveness Detection Linux SDK](../../products/face-recognition-sdk/liveness-detection-sdk/server-sdk/liveness-detection-linux-sdk.md) · [Face Liveness Detection Windows SDK](../../products/face-recognition-sdk/liveness-detection-sdk/server-sdk/liveness-detection-windows-sdk.md).

### Try it

```bash
curl -s -X POST http://127.0.0.1:8084/api/liveness \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$(base64 -w0 face.jpg)\"}"
```

### `POST /api/liveness`

Alias: `POST /api/check_liveness`.

{% tabs %}
{% tab title="JSON" %}
```json
{ "image": "<BASE64-JPEG>", "algorithm": "all" }
```
{% endtab %}

{% tab title="form-data" %}
File field `image` (alias `file`). Optional text field `algorithm`.
{% endtab %}
{% endtabs %}

|                  |                                                                                         |
| ---------------- | --------------------------------------------------------------------------------------- |
| **Input**        | JPEG image. A missing `image` field returns envelope `code: -1` and `"image required"`. |
| **Return value** | Engine JSON. Score **≥ 0.5** means Real / `pass` true.                                  |

Example shape:

```json
{ "score": 0.72, "result": "Real", "pass": true }
```

Python:

```python
import sdk
sdk.activate("license.txt")
sdk.init_sdk()
print(sdk.liveness(base64_jpeg))
```

### Related documentation

* [HTTP API overview](./) · [Face Recognition HTTP API](face-recognition.md) · [Hosting requirements](../deploy-and-host/hosting-requirements.md)
