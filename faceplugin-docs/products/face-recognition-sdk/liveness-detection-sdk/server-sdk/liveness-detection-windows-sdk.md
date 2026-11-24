---
description: >-
  Faceplugin Face Liveness Detection Windows SDK. Fully on-premise PAD HTTP API
  on port 8084. POST /api/liveness.
icon: windows
---

# Windows

Fully on-premise **Face Liveness HTTP API for Windows**. Default port **8084**. Gradio **9004**. No Docker on Windows.

Score **one RGB JPEG**. Score **≥ 0.5** → Real / `pass` true. Same HTTP API as [Face Liveness Detection Linux SDK](liveness-detection-linux-sdk.md). `POST /api/check_liveness` is an alias of `/api/liveness`.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceLivenessDetection-Windows" %}

### Setup <a href="#setup" id="setup"></a>

Sizing: [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md).

{% stepper %}
{% step %}
#### Copy the runtime

Copy the CPU libraries from [Google Drive](https://drive.google.com/drive/folders/11xD987eHT00NUGiJZCNYSvwRadi0Nue5) into `lib\cpu\`.
{% endstep %}

{% step %}
#### Install and run

```
pip install -r requirements.txt
run.bat
```
{% endstep %}

{% step %}
#### Copy the machine code

```
curl -s http://127.0.0.1:8084/api/machinecode
```

Send machine code to Faceplugin.
{% endstep %}

{% step %}
#### Activate, then check liveness

```
curl -s -X POST http://127.0.0.1:8084/api/activate \
  -H 'Content-Type: text/plain' \
  --data-binary @license.txt

IMG=$(base64 -w0 face.jpg)
curl -s -X POST http://127.0.0.1:8084/api/liveness \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$IMG\"}"
```
{% endstep %}
{% endstepper %}

### APIs

| Endpoint                   | Purpose                           |
| -------------------------- | --------------------------------- |
| `GET /api/health`          | Process is listening (no license) |
| `GET /api/machinecode`     | Machine code                      |
| `GET /api/licenseStatus`   | License status                    |
| `GET /api/backend`         | `"cpu"`                           |
| `POST /api/activate`       | Activate and `init_sdk()`         |
| `POST /api/liveness`       | Passive face anti-spoofing        |
| `POST /api/check_liveness` | Alias of `/api/liveness`          |

Full reference: [Face Liveness HTTP API](../../../../integration/http-api/face-liveness.md). Shared control routes: [Shared endpoints](../../../../integration/http-api/shared.md).

Some routes wrap the result in a small JSON object (an **envelope**). `POST /api/liveness` returns **engine JSON** as the body. Score **≥ 0.5** → Real / `pass` true.

Python:

```python
import sdk

sdk.get_machine_code()
sdk.activate("license.txt")
sdk.init_sdk()
print(sdk.liveness(base64_jpeg))
```

### Try it

```bash
curl -s http://127.0.0.1:8084/api/health
IMG=$(base64 -w0 face.jpg)

curl -s -X POST http://127.0.0.1:8084/api/liveness \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$IMG\"}"
```

**Postman:** import `postman/FaceLiveness-API.postman_collection.json` from the repo. Base URL `http://127.0.0.1:8084`.

**Gradio (host only):** `DEMO_PORT=9004 API_BASE=http://127.0.0.1:8084 python demo.py`.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Which liveness API does the Windows SDK use?**

The same as Linux: `POST /api/liveness` on port 8084. Docker is not used on Windows.

**Does it support active liveness detection?**

No. This product does passive anti-spoofing on a single JPEG only, with no challenge for the user.

**What score counts as a real face?**

A liveness score of 0.5 or higher counts as Real (`pass` is `true`).

**Do I need Docker to run liveness detection on Windows?**

No. The Windows SDK runs natively with Python and `run.bat`.

**Can I run face recognition and liveness on the same Windows server?**

Yes. Run the Face Recognition Windows SDK on port 8083 and the Face Liveness Windows SDK on port 8084 as separate processes.

### Related documentation

* [Faceplugin Face Liveness Detection Server SDK](./) · [Linux](liveness-detection-linux-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition Windows SDK](../../recognition/server-sdk/face-recognition-windows-sdk.md)
* [ID Document Recognition Windows SDK](../../../id-document-recognition-sdk/recognition-and-liveness/server-sdk/id-document-recognition-windows-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
