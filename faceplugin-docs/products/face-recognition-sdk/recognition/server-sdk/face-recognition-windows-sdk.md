---
description: >-
  Faceplugin Face Recognition Windows SDK. Fully on-premise HTTP API on port
  8083. detect, quality, feature, match, similarity.
icon: windows
---

# Windows

Fully on-premise **commercial Face Recognition HTTP API for Windows**. Default port **8083**. Gradio **9003**. No Docker on Windows.

This is not the [Open Source Face Recognition Windows SDK](../open-source-windows-and-linux/open-source-face-recognition-windows-sdk.md). For recognition and liveness in the **same** process, use [Face Recognition + Liveness Windows SDK](../../recognition-and-liveness/recognition-and-liveness-server/face-recognition-sdk-windows.md). Or run [Face Liveness Detection Windows SDK](../../liveness-detection-sdk/server-sdk/liveness-detection-windows-sdk.md) on port **8084** as a second process.

All processing stays on your server. **No** biometric data is sent to Faceplugin cloud.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-Windows" %}

### Setup <a href="#setup" id="setup"></a>

Sizing: [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md).

{% stepper %}
{% step %}
#### Copy the runtime

Copy the CPU libraries from [Google Drive](https://drive.google.com/drive/folders/12i5d2-TahuJumre2EVYqWO8cIi_unTBz) into `lib\cpu\`.
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
curl -s http://127.0.0.1:8083/api/machinecode
```

Send machine code to Faceplugin. Copy it from the terminal if curl is not available.
{% endstep %}

{% step %}
#### Activate

```
curl -s -X POST http://127.0.0.1:8083/api/activate \
  -H 'Content-Type: text/plain' \
  --data-binary @license.txt
```
{% endstep %}
{% endstepper %}

{% hint style="info" %}
Control routes (`/api/health`, `/api/machinecode`, `/api/activate`, `/api/licenseStatus`) return a JSON **envelope**. Process POSTs (`/api/detect`, `/api/quality`, …) return **engine JSON** as the HTTP body.
{% endhint %}

### APIs

Same routes as [Face Recognition Linux SDK](face-recognition-linux-sdk.md):

| Endpoint                 | Purpose                           |
| ------------------------ | --------------------------------- |
| `GET /api/health`        | Process is listening (no license) |
| `GET /api/machinecode`   | Machine code                      |
| `GET /api/licenseStatus` | License status                    |
| `GET /api/backend`       | `"cpu"`                           |
| `POST /api/activate`     | Activate and `init_sdk()`         |
| `POST /api/detect`       | Detect faces                      |
| `POST /api/quality`      | Quality checks                    |
| `POST /api/feature`      | Extract template                  |
| `POST /api/match`        | Compare two photos                |
| `POST /api/similarity`   | Compare two templates             |

Full reference: [Face Recognition HTTP API](../../../../integration/http-api/face-recognition.md). Shared control routes: [Shared endpoints](../../../../integration/http-api/shared.md).

Some routes wrap the result in a small JSON object (an **envelope**). Process POSTs return **engine JSON** as the body. There is **no** `POST /api/identify` (no server-side 1:N gallery).

### Try it

```bash
curl -s http://127.0.0.1:8083/api/health
IMG=$(base64 -w0 face.jpg)

curl -s -X POST http://127.0.0.1:8083/api/detect \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$IMG\"}"

curl -s -X POST http://127.0.0.1:8083/api/match \
  -H 'Content-Type: application/json' \
  -d "{\"image1\":\"$IMG\",\"image2\":\"$IMG\"}"
```

**Postman:** import `postman/FaceRecognition-API.postman_collection.json` from the repo. Base URL `http://127.0.0.1:8083`. Paths have **no** `/v1`.

**Gradio (host only):** `DEMO_PORT=9003 API_BASE=http://127.0.0.1:8083 python demo.py`. Open [http://127.0.0.1:9003](http://127.0.0.1:9003). You do **not** need Gradio in production.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Is there a face recognition API for Windows?**

Yes. The Face Recognition Windows SDK serves an HTTP API on port 8083 with the same routes as the Linux version, and it doesn't need Docker.

**Does the Windows face recognition SDK work offline?**

Yes. After you call `POST /api/activate` with a license key bound to the host's machine code, it runs with no internet connection.

**Does the Windows face recognition server support 1:N search?**

No. There is no `POST /api/identify` endpoint. Store face templates in your own database and compare them with `/api/similarity`.

**Do I need Docker to run face recognition on Windows?**

No. The Windows SDK runs natively with Python and `run.bat`. Docker is only used for the Linux version.

**Is this the same as the open-source Windows SDK?**

No. This is Faceplugin's commercial Windows SDK. The open-source Face Recognition Windows SDK is a separate product.

### Related documentation

* [Faceplugin Face Recognition Server SDK](./) · [Linux](face-recognition-linux-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Liveness Detection Windows SDK](../../liveness-detection-sdk/server-sdk/liveness-detection-windows-sdk.md)
* [ID Document Recognition Windows SDK](../../../id-document-recognition-sdk/recognition-and-liveness/server-sdk/id-document-recognition-windows-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
