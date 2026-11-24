---
description: >-
  Faceplugin ID Document Recognition Windows SDK. Fully on-premise OCR and
  authenticity HTTP API on port 8082. documentRecognition, documentProcess.
icon: windows
---

# Windows

Fully on-premise **ID Document Recognition HTTP API for Windows**. Default port **8082**. Gradio **9002**. No Docker on Windows — use [Linux SDK](id-document-recognition-linux-sdk.md) for Docker.

Reads ID cards, passports, and driver licenses. Same routes as [ID Document Recognition Linux SDK](id-document-recognition-linux-sdk.md). Parse results with [Document result JSON](../../document-result-json.md).

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/ID-Document-Recognition-Windows" %}

### Setup <a href="#setup" id="setup"></a>

Sizing: [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md).

{% stepper %}
{% step %}
#### Copy the runtime

Copy the CPU libraries from [Google Drive](https://drive.google.com/drive/folders/1YfHUwnXO0E2NSvS_81nTNO2z3mKVO85g) into `lib\cpu\`.
{% endstep %}

{% step %}
#### Install and run

```
pip install -r requirements.txt
run.bat
```

Demo UI: `run_demo.bat` after the API is up.
{% endstep %}

{% step %}
#### Copy the machine code and activate

```
curl -s http://127.0.0.1:8082/api/machinecode
curl -s -X POST http://127.0.0.1:8082/api/activate \
  -H 'Content-Type: text/plain' \
  --data-binary @license.txt
```
{% endstep %}
{% endstepper %}

### APIs

Same as [ID Document Recognition Linux SDK](id-document-recognition-linux-sdk.md):

| Endpoint                        | Purpose                                  |
| ------------------------------- | ---------------------------------------- |
| `GET /api/health`               | Process is listening (no license)        |
| `GET /api/machinecode`          | Machine code                             |
| `GET /api/licenseStatus`        | License status                           |
| `GET /api/backend`              | `"cpu"`                                  |
| `POST /api/activate`            | Activate and `init_sdk()`                |
| `POST /api/documentRecognition` | OCR / MRZ / barcode / image quality only |
| `POST /api/documentLiveness`    | Authenticity only                        |
| `POST /api/documentProcess`     | Combined OCR + optional authenticity     |
| `POST /api/generalProcess`      | Single-image general process             |

Full reference: [Document Reader HTTP API](../../../../integration/http-api/document-reader.md). Shared control routes: [Shared endpoints](../../../../integration/http-api/shared.md).

Some routes wrap the result in a small JSON object (an **envelope**). Process POSTs return **engine JSON** as the body.

### Try it

```bash
curl -s http://127.0.0.1:8082/api/health
IMG=$(base64 -w0 id-front.jpg)

curl -s -X POST http://127.0.0.1:8082/api/documentProcess \
  -H 'Content-Type: application/json' \
  -d "{\"images\":[{\"image\":\"$IMG\",\"page_idx\":0}],\"response\":{\"OCR\":\"normal\",\"Authenticity\":\"normal\"}}"
```

**Postman:** import `postman/DocumentReader-API.postman_collection.json` from the repo. Base URL `http://127.0.0.1:8082`. Gradio **9002**. Authenticity names: [Document security check fields](../../document-security-check-fields.md).

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Is there an ID document recognition API for Windows?**

Yes. It uses the same routes as the Linux SDK, on port 8082, and runs without Docker.

**How do I run document authenticity checks on Windows?**

Use `documentLiveness` or `documentProcess`. Both need a license that includes authenticity.

**Do I need Docker to run it on Windows?**

No. The Windows SDK runs natively with `run.bat`. Docker is used only for the Linux SDK.

**Which documents does it read?**

ID cards, passports and driver's licenses.

**Does it send document data to the cloud?**

No. It is fully on-premise and runs on your own Windows machine.

### Related documentation

* [Faceplugin ID Document Recognition Server SDK](./) · [Linux](id-document-recognition-linux-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition Windows SDK](../../../face-recognition-sdk/recognition/server-sdk/face-recognition-windows-sdk.md)
* [Face Liveness Detection Windows SDK](../../../face-recognition-sdk/liveness-detection-sdk/server-sdk/liveness-detection-windows-sdk.md)
* [Document result JSON](../../document-result-json.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
