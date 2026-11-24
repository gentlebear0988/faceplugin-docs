---
description: >-
  Faceplugin document verification API for Linux Docker. On-premise passport
  OCR, MRZ, and authenticity. POST /api/documentProcess on port 8082.
icon: linux
---

# Linux

Fully on-premise **ID Document Recognition HTTP API for Linux / Docker**. Image: `faceplugin/document-reader`. Default port **8082**. Gradio **9002**.

Reads ID cards, passports, and driver licenses. OCR, MRZ, barcode / QR, image quality, crops, and optional authenticity (document liveness).

All processing stays on your server. **No** biometric data is sent to Faceplugin cloud.

{% hint style="warning" %}
`--shm-size=2gb` is required. A smaller shm size can crash the engine.
{% endhint %}

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/ID-Document-Recognition-Docker" %}

### Setup <a href="#setup" id="setup"></a>

Sizing: [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md).

{% stepper %}
{% step %}
#### Pull and run the Docker image

The image comes straight from Docker Hub, so there is nothing to download from Google Drive.

```
sudo docker pull faceplugin/document-reader:latest
sudo docker run -d --name faceplugin-document-reader \
  --shm-size=2gb --privileged \
  -p 8082:8082 \
  -v /etc/machine-id:/etc/machine-id:ro \
  faceplugin/document-reader:latest
```

> **`--shm-size=2gb` is required.** A smaller value can crash the engine.

> **Docker Desktop (macOS or Windows):** leave out the `-v /etc/machine-id:/etc/machine-id:ro` line.
{% endstep %}

{% step %}
**Check that the API is running**

```
curl -s http://127.0.0.1:8082/api/health
```
{% endstep %}

{% step %}
**Get your machine code and activate**

```
curl -s http://127.0.0.1:8082/api/machinecode
curl -s -X POST http://127.0.0.1:8082/api/activate \
  -H 'Content-Type: text/plain' \
  --data-binary @license.txt
```
{% endstep %}
{% endstepper %}

### Run multiple containers with one license <a href="#run-multiple-containers" id="run-multiple-containers"></a>

You only need this section if you want to run multiple Document Reader containers on the same Linux host.

On Linux, mount `/etc/machine-id` into each container so they use the same machine code. Each container must have a different container name and host port.

For example:

```bash
sudo docker run -d --name faceplugin-document-reader-2 \
  --shm-size=2gb --privileged \
  -p 8083:8082 \
  -v /etc/machine-id:/etc/machine-id:ro \
  faceplugin/document-reader:latest
```

Activate each container with the same license key (`POST /api/activate` on each host port).

On Docker Desktop (macOS/Windows), omit the `/etc/machine-id` volume. Each container may require its own license.

### Other ways to run

#### Option B — Docker Compose (local build)

```
git clone https://github.com/Faceplugin-ltd/ID-Document-Recognition-Docker.git
cd ID-Document-Recognition-Docker
```

Put the [Google Drive runtime](https://drive.google.com/drive/folders/16DFGKtyGbyL-0gfVOmNVaQ9vgXCYDr2M) files **directly** in `lib/cpu/`, then:

```
# macOS/Windows Docker Desktop: remove the /etc/machine-id volume from docker-compose.yml first
sudo docker compose up --build -d
sudo docker compose logs -f
```

#### Option C — Native Linux (no Docker)

Same `lib/cpu/` layout:

```
./run.sh
```

**Try online:** [Hugging Face Space](https://huggingface.co/spaces/Faceplugin-Ltd/ID-Document-Recognition-SDK) (Gradio UI → your Linux API).

### License

[Request a License & Support](../../../../help/request-a-license-and-support.md).

### Try it

```bash
curl -s http://127.0.0.1:8082/api/health
IMG=$(base64 -w0 id-front.jpg)

curl -s -X POST http://127.0.0.1:8082/api/documentProcess \
  -H 'Content-Type: application/json' \
  -d "{\"images\":[{\"image\":\"$IMG\",\"page_idx\":0}],\"response\":{\"OCR\":\"normal\",\"Authenticity\":\"normal\"}}"
```

**Postman:** import `postman/DocumentReader-API.postman_collection.json` from the repo. Base URL `http://127.0.0.1:8082`.

**Gradio (host only):**

```
pip3 install -r requirements-demo.txt
DEMO_PORT=9002 API_BASE=http://127.0.0.1:8082 python3 demo.py
```

Open [http://127.0.0.1:9002](http://127.0.0.1:9002). Tabs: Result, Liveness, Images, Raw JSON.

Parse the engine JSON with [Document result JSON](../../document-result-json.md).

### Integrate into your own app

| Path         | When to use                                                 |
| ------------ | ----------------------------------------------------------- |
| **HTTP**     | Any language. Keep this API running and `POST` page images. |
| **`sdk.py`** | Python on the same Linux host as `lib/cpu/`.                |

You do **not** need Gradio in production.

### APIs

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

Some routes wrap the result in a small JSON object (an **envelope**). Process POSTs return **engine JSON** as the body. Authenticity-only without OCR: [ID Document Liveness Linux SDK](../../id-document-liveness-sdk/server-sdk/id-document-liveness-linux-sdk.md).

Python:

```python
import sdk

sdk.activate("license.txt")
sdk.init_sdk()
print(sdk.document_recognition([{"image": base64_front}]))
print(sdk.document_process(images, options={"response": {"OCR": "normal", "Authenticity": "normal"}}))
```

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**What is the document verification API?**

`POST /api/documentRecognition` and `POST /api/documentProcess` on port 8082, served by the `faceplugin/document-reader` Docker image.

**Does it do passport OCR?**

Yes. OCR, MRZ, barcode data and image crops come back in the same JSON response.

**How do I turn on authenticity checks?**

Call `documentProcess` with `"Authenticity": "normal"` in the `response` options, as in the test request above.

**Why does my container crash on start?**

Check that you ran it with `--shm-size=2gb`. A smaller shared-memory size can crash the engine.

**Can I run several containers with one license?**

Yes. Give each container its own name and host port; they can share the same machine ID and license.

**Does the API send document data to the cloud?**

No. It is fully on-premise and runs on your own server.

### Related documentation

* [Faceplugin ID Document Recognition Server SDK](./) · [Windows](id-document-recognition-windows-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition Linux SDK](../../../face-recognition-sdk/recognition/server-sdk/face-recognition-linux-sdk.md)
* [Face Liveness Detection Linux SDK](../../../face-recognition-sdk/liveness-detection-sdk/server-sdk/liveness-detection-linux-sdk.md)
* [ID Document Liveness Linux SDK](../../id-document-liveness-sdk/server-sdk/id-document-liveness-linux-sdk.md)
* [Document result JSON](../../document-result-json.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
