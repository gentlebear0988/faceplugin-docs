---
description: >-
  Faceplugin Face Recognition SDK Windows — recognition and face liveness in one
  process. HTTP API on port 8083 including POST /api/liveness. No Docker.
icon: windows
---

# Windows

Fully on-premise **Face Recognition + Face Liveness HTTP API for Windows** in **one** process. Default port **8083**. Gradio **9003**. No Docker on Windows.

One wrapper (`FaceRecognitionSDK.dll`), one license, two model packs (`far.fpk` + `fal.fpk`). Your license can unlock Recognition only, Liveness only, or both.

This is **not** the recognition-only [Face Recognition Windows SDK](../../recognition/server-sdk/face-recognition-windows-sdk.md). It is **not** the anti-spoofing-only [Face Liveness Detection Windows SDK](../../liveness-detection-sdk/server-sdk/liveness-detection-windows-sdk.md) (port **8084**). Do not copy two products’ Google Drive runtimes into one `lib\cpu\` folder.

For Docker combined, use [Face Recognition + Liveness Linux SDK](face-recognition-sdk-linux.md) (`faceplugin/face-recognition-liveness-sdk`).

All processing stays on your machine. **No** biometric data is sent to Faceplugin cloud.

### Code <a href="#code" id="code"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognitionSDK-Windows" %}

### Setup <a href="#setup" id="setup"></a>

Sizing: [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md).

{% stepper %}
{% step %}
#### Copy the runtime

Copy the CPU libraries from Google Drive into `lib\cpu\` (see the repo README for the current folder link).
{% endstep %}

{% step %}
#### Install and run

```
pip install -r requirements.txt
run.bat
```
{% endstep %}

{% step %}
#### Get your machine code

```
curl -s http://127.0.0.1:8083/api/machinecode
```

Send machine code to Faceplugin. Copy it from the terminal if curl is not available.
{% endstep %}

{% step %}
#### Activate the license

```
curl -s -X POST http://127.0.0.1:8083/api/activate \
  -H 'Content-Type: text/plain' \
  --data-binary @license.txt
```
{% endstep %}
{% endstepper %}

{% hint style="info" %}
Some routes wrap the result in a small JSON object (an **envelope**). Process POSTs return **engine JSON** as the body. Port **8083** is shared with recognition-only — run **one** Face Recognition HTTP service at a time.
{% endhint %}

### APIs

Same recognition routes as [Face Recognition Windows SDK](../../recognition/server-sdk/face-recognition-windows-sdk.md), **plus** liveness:

| Endpoint                 | Purpose                           |
| ------------------------ | --------------------------------- |
| `GET /api/health`        | Process is listening (no license) |
| `GET /api/machinecode`   | Machine code                      |
| `GET /api/licenseStatus` | License capabilities              |
| `GET /api/backend`       | `"cpu"`                           |
| `POST /api/activate`     | Activate and init                 |
| `POST /api/detect`       | Detect faces                      |
| `POST /api/quality`      | Quality checks                    |
| `POST /api/feature`      | Extract template                  |
| `POST /api/match`        | Compare two photos                |
| `POST /api/similarity`   | Compare two templates             |
| `POST /api/liveness`     | Passive face anti-spoofing        |

Full reference: [Face Recognition HTTP API](../../../../integration/http-api/face-recognition.md). Liveness: [Face Liveness HTTP API](../../../../integration/http-api/face-liveness.md). Shared control routes: [Shared endpoints](../../../../integration/http-api/shared.md).

There is **no** `POST /api/identify` (no server-side 1:N gallery).

### Try it

```bash
curl -s http://127.0.0.1:8083/api/health
IMG=$(base64 -w0 face.jpg)

curl -s -X POST http://127.0.0.1:8083/api/detect \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$IMG\"}"

curl -s -X POST http://127.0.0.1:8083/api/liveness \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$IMG\"}"
```

Local Gradio: `run_demo.bat` on **9003** (Detect, Quality, Match, **Liveness**).

### Frequently Asked Questions

**Is this one Windows process for both face recognition and liveness?**

Yes. A single process on port 8083 serves the recognition routes and `POST /api/liveness`.

**Can I run recognition and liveness as separate products instead?**

Yes. Use the Face Recognition Windows SDK on port 8083 and the Face Liveness Windows SDK on port 8084.

**Do I need Docker on Windows?**

No. The combined SDK runs natively on Windows. For Docker, use the Linux combined SDK.

**Does one license cover both recognition and liveness?**

The package uses one license, which can enable recognition only, liveness only, or both.

**Does the combined server support 1:N face search?**

No. There is no `POST /api/identify`. Store templates in your own database and compare them with `/api/similarity`.

### Related documentation

* [Face Recognition Windows SDK](../../recognition/server-sdk/face-recognition-windows-sdk.md) (recognition only) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition + Liveness Linux SDK](face-recognition-sdk-linux.md)
* [Face Liveness Detection Windows SDK](../../liveness-detection-sdk/server-sdk/liveness-detection-windows-sdk.md)
* [Face Recognition Server SDK](../../recognition/server-sdk/) · [Choose a product](../../../../getting-started/choose-a-product.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
