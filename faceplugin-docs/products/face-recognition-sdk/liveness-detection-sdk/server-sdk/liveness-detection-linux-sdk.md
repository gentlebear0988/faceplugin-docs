---
description: >-
  Faceplugin Face Liveness API for Linux Docker. On-premise anti-spoofing PAD.
  POST /api/liveness on port 8093. Score 0.5 or higher is Real.
icon: linux
---

# Linux

Fully on-premise **Face Liveness HTTP API for Linux / Docker**. Image: `faceplugin/face-liveness`. Default port **8093**.

Score **one RGB JPEG**. Score **≥ 0.5** → Real / `pass` true. Score **< 0.5** → Spoof / `pass` false.

Need recognition as well? Run [Face Recognition Linux SDK](../../recognition/server-sdk/face-recognition-linux-sdk.md) on port **8092** as a **second** container.

All processing stays on your server. **No** biometric data is sent to Faceplugin cloud.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceLivenessDetection-Docker" %}

### Setup <a href="#setup" id="setup"></a>

Sizing: [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md).

{% stepper %}
{% step %}
#### Pull and run the Docker image

```
sudo docker pull faceplugin/face-liveness:latest
sudo docker run -d --name faceplugin-face-liveness \
  --shm-size=2gb --privileged \
  -p 8093:8093 \
  -v /etc/machine-id:/etc/machine-id:ro \
  faceplugin/face-liveness:latest
```

{% hint style="warning" %}
**All three flags are mandatory.** The image refuses a plain `docker run`. On start-up it checks that `/etc/machine-id` is a bind mount, that `/dev/shm` is at least ~1.9 GB, and that `CAP_SYS_ADMIN` is set — and exits with an error if any is missing. Without the mount the licence would bind to the container's own machine id rather than the host's, so the machine code you were issued a key against would never match.

This means the image expects a **Linux host**, or a Linux VM that can satisfy those three conditions. Dropping the `/etc/machine-id` mount on Docker Desktop does not work around the check; the container exits.
{% endhint %}
{% endstep %}

{% step %}
#### Check that the API is running

```
curl -s http://127.0.0.1:8093/api/health
```
{% endstep %}

{% step %}
#### Get your machine code

```
curl -s http://127.0.0.1:8093/api/machinecode
```

Send machine code to Faceplugin. Docker and local host codes are **different**.
{% endstep %}

{% step %}
#### Activate the license

Put your key in `license.txt`, then:

```
curl -s -X POST http://127.0.0.1:8093/api/activate \
  -H 'Content-Type: text/plain' \
  --data-binary @license.txt
```
{% endstep %}

{% step %}
#### Check a face for liveness

```
IMG=$(base64 -w0 face.jpg)   # macOS: base64 -i face.jpg

curl -s -X POST http://127.0.0.1:8093/api/liveness \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$IMG\"}"
```
{% endstep %}
{% endstepper %}

### Run multiple containers with one license <a href="#run-multiple-containers" id="run-multiple-containers"></a>

You only need this section if you want to run multiple Face Liveness containers on the same Linux host.

On Linux, mount `/etc/machine-id` into each container so they use the same machine code. Each container must have a different container name and host port.

For example:

```bash
sudo docker run -d --name faceplugin-face-liveness-2 \
  --shm-size=2gb --privileged \
  -p 8085:8093 \
  -v /etc/machine-id:/etc/machine-id:ro \
  faceplugin/face-liveness:latest
```

Activate each container with the same license key (`POST /api/activate` on each host port).

Every container needs the same three flags as the first one — the start-up check applies to each.

### Other ways to run

#### Option B — Docker Compose (local build)

```
git clone https://github.com/Faceplugin-ltd/FaceLivenessDetection-Docker.git
cd FaceLivenessDetection-Docker
```

Put the [Google Drive runtime](https://drive.google.com/drive/folders/1rFnw7VASLmA4q8NWenQgszFS8njRGEgt) files **directly** in `lib/cpu/`, then:

```
sudo docker compose up --build -d
sudo docker compose logs -f
```

#### Option C — Native Linux (no Docker)

Same `lib/cpu/` layout:

```
./run.sh
```

Default port **8093**. Gradio demo (`demo.py`) on **9093** (host only). The Docker Hub image is API-only.

`POST /api/check_liveness` is an alias of `/api/liveness`.

{% hint style="info" %}
Control routes (`/api/health`, `/api/machinecode`, `/api/activate`, `/api/licenseStatus`, `/api/backend`) return the Flask **envelope** `{"success", "code", "message", "request_id", "data"}`. `POST /api/liveness` passes the engine's own JSON through verbatim — which carries the same `success` / `code` / `message` / `data` keys, so you read the score the same way.
{% endhint %}

### License

Licenses are **offline**. [Request a License & Support](../../../../help/request-a-license-and-support.md).

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

Control routes return the Flask **envelope**. `POST /api/liveness` passes the engine's own JSON through verbatim, and that JSON is shaped the same way:

```json
{ "success": true, "code": 0, "message": "OK",
  "data": { "score": 0.72, "result": "Real", "pass": true } }
```

`pass` is `score >= 0.5`, and `result` is `"Real"` or `"Spoof"`. That cut-off is fixed in native code and is **not** configurable over HTTP — apply your own to `score` if you need a different one.

Failures come back the same way: no face gives `code` **2** with `message` `"NO_FACE"`, another engine error gives **-19**, a missing or empty `image` gives **-19** with `"Image not suitable"`, and a key without liveness gives **8** with a `licenseError`.

Python:

```python
import sdk

machine_code = sdk.get_machine_code()
sdk.activate("license.txt")
sdk.init_sdk()
print(sdk.liveness(base64_jpeg))
```

### Try it

```bash
curl -s http://127.0.0.1:8093/api/health
IMG=$(base64 -w0 face.jpg)

curl -s -X POST http://127.0.0.1:8093/api/liveness \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$IMG\"}"
```

**Postman:** import `postman/FaceLiveness-API.postman_collection.json` from the repo. Base URL `http://127.0.0.1:8093`.

**Gradio (host only):**

```
pip3 install -r requirements-demo.txt
DEMO_PORT=9093 API_BASE=http://127.0.0.1:8093 python3 demo.py
```

Open [http://127.0.0.1:9093](http://127.0.0.1:9093). You do **not** need Gradio in production.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**What is the Faceplugin face liveness API?**

`POST /api/liveness` on port 8093, served by the `faceplugin/face-liveness` Docker image. A score of 0.5 or higher means the face is real.

**How do I get the machine code for my license?**

Call `GET /api/machinecode`. The machine code inside Docker differs from the host's, so request it from the environment you will run.

**Is this active or passive liveness detection?**

Passive. The API scores a single JPEG, with no challenge for the user. Active liveness — an anti-spoofing gate followed by a shuffled challenge sequence — is part of the [Mobile SDK](../mobile-sdk/).

**Can I choose a lighter or faster model?**

No. There is no algorithm or model parameter. The runtime fuses its own estimators into the one score it returns.

**Can I run face recognition and liveness on the same Linux server?**

Yes. Run Face Recognition on port 8092 and Face Liveness on port 8093 as separate containers.

**Can I run several liveness containers with one license?**

Yes, on the same Linux host. Mount `/etc/machine-id` into each container and give each a unique name and host port (for example 8085).

### Related documentation

* [Faceplugin Face Liveness Detection Server SDK](./) · [Windows](liveness-detection-windows-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition Linux SDK](../../recognition/server-sdk/face-recognition-linux-sdk.md)
* [ID Document Recognition Linux SDK](../../../id-document-recognition-sdk/recognition-and-liveness/server-sdk/id-document-recognition-linux-sdk.md)
* [ID Document Liveness Linux SDK](../../../id-document-recognition-sdk/id-document-liveness-sdk/server-sdk/id-document-liveness-linux-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
