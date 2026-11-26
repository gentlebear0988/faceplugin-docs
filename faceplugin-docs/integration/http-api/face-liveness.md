---
description: >-
  Faceplugin Face Liveness HTTP API. POST /api/liveness (alias
  /api/check_liveness) with a JPEG on port 8093. Score 0.5 or higher is Real.
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

It runs on Docker image `faceplugin/face-liveness` and on the Windows app. Default port **8093**. The engine is **CPU only**.

The HTTP API is **passive only**: it scores one finished image and asks nothing of the user. Active liveness — an anti-spoofing gate followed by a shuffled challenge sequence — exists only in the mobile SDKs.

Shared routes: [Shared endpoints](shared.md). A call without a liveness license returns `code` 8 with `licenseError`.

Face Recognition runs as its own service on port **8092** and does not expose `/api/liveness`. See [Face Recognition HTTP API](face-recognition.md).

Install and run: [Face Liveness Detection Linux SDK](../../products/face-recognition-sdk/liveness-detection-sdk/server-sdk/liveness-detection-linux-sdk.md) · [Face Liveness Detection Windows SDK](../../products/face-recognition-sdk/liveness-detection-sdk/server-sdk/liveness-detection-windows-sdk.md).

### Try it

```bash
curl -s -X POST http://127.0.0.1:8093/api/liveness \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$(base64 -w0 face.jpg)\"}"
```

### `POST /api/liveness`

Alias: `POST /api/check_liveness`.

{% tabs %}
{% tab title="JSON" %}
```json
{ "image": "<BASE64-JPEG>" }
```
{% endtab %}

{% tab title="form-data" %}
File field `image` (alias `file`).
{% endtab %}
{% endtabs %}

`image` is the only field the route reads. There is no algorithm, model or threshold parameter.

|                  |                                                                                                       |
| ---------------- | ------------------------------------------------------------------------------------------------------- |
| **Input**        | JPEG image. A missing or empty `image` field returns `code: -19` and `"Image not suitable"`.           |
| **Return value** | The engine JSON, passed through verbatim. It already carries the envelope fields. Score **≥ 0.5** means Real / `pass` true. |

Success:

```json
{
  "success": true,
  "code": 0,
  "message": "OK",
  "data": { "score": 0.72, "result": "Real", "pass": true }
}
```

`result` is `"Real"` or `"Spoof"`, and `pass` is `score >= 0.5`. The 0.5 verdict threshold is hard-coded in the native engine and cannot be changed over HTTP.

Failure shapes:

```json
{ "success": false, "code": 2, "message": "NO_FACE", "data": null, "status": 2, "result": "NO_FACE" }
```

```json
{ "success": false, "code": -19, "message": "Image not suitable", "data": null }
```

```json
{ "success": false, "code": 8, "message": "This license does not include liveness", "licenseError": "…", "data": null }
```

Any other engine error reports `code` and `status` `-19`.

The remaining routes — `GET /api/health`, `/api/machinecode`, `/api/licenseStatus`, `/api/backend` and `POST /api/activate` — use the shared envelope `{"success","code","message","request_id","data"}`, where `request_id` is always `null`. `GET /api/backend` answers:

```json
{ "product": "FaceLivenessSDK", "sdk_version": "1.0.0", "backend": "cpu", "lib_dir": "lib/cpu" }
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
