---
description: >-
  Faceplugin Face Recognition API for Linux Docker. On-premise detect, match,
  and similarity on port 8083. Offline after license activation.
icon: linux
---

# Linux

Fully on-premise **Face Recognition API for Linux / Docker**. Image: `faceplugin/face-recognition`. Default port **8083**.

This product is **recognition only**. For recognition and liveness in the **same** container, use [Face Recognition + Liveness Linux SDK](../../recognition-and-liveness/recognition-and-liveness-server/face-recognition-sdk-linux.md) (`faceplugin/face-recognition-liveness-sdk`). Or run [Face Liveness Detection Linux SDK](../../liveness-detection-sdk/server-sdk/liveness-detection-linux-sdk.md) on port **8084** as a second container.

All processing stays on your server. **No** biometric data is sent to Faceplugin cloud.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-Docker" %}

### Setup <a href="#setup" id="setup"></a>

Sizing: [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md).

{% stepper %}
{% step %}
#### Pull from Docker Hub (no Google Drive download)

```
sudo docker pull faceplugin/face-recognition:latest
sudo docker run -d --name faceplugin-face-recognition \
  --shm-size=2gb --privileged \
  -p 8083:8083 \
  -v /etc/machine-id:/etc/machine-id:ro \
  faceplugin/face-recognition:latest
```

On Docker Desktop (macOS/Windows) omit the `/etc/machine-id` volume.
{% endstep %}

{% step %}
#### Confirm health (no license yet)

```
curl -s http://127.0.0.1:8083/api/health
```
{% endstep %}

{% step %}
#### Copy the machine code

```
curl -s http://127.0.0.1:8083/api/machinecode
```

Send machine code to Faceplugin. Docker and local host codes are **different**.
{% endstep %}

{% step %}
#### Activate

Put your key in `license.txt`, then:

```
curl -s -X POST http://127.0.0.1:8083/api/activate \
  -H 'Content-Type: text/plain' \
  --data-binary @license.txt
```
{% endstep %}
{% endstepper %}

### Run multiple containers with one license <a href="#run-multiple-containers" id="run-multiple-containers"></a>

You only need this section if you want to run multiple Face Recognition containers on the same Linux host.

On Linux, mount `/etc/machine-id` into each container so they use the same machine code. Each container must have a different container name and host port.

For example:

```bash
sudo docker run -d --name faceplugin-face-recognition-2 \
  --shm-size=2gb --privileged \
  -p 8084:8083 \
  -v /etc/machine-id:/etc/machine-id:ro \
  faceplugin/face-recognition:latest
```

Activate each container with the same license key (`POST /api/activate` on each host port).

On Docker Desktop (macOS/Windows), omit the `/etc/machine-id` volume. Each container may require its own license.

### Other ways to run

#### Option B — Docker Compose (local build)

```
git clone https://github.com/Faceplugin-ltd/FaceRecognition-Docker.git
cd FaceRecognition-Docker
```

Put the [Google Drive runtime](https://drive.google.com/drive/folders/1NVq0psW8PLfEX58FWNE-RKFWfCZdOMmz) files **directly** in `lib/cpu/`, then:

```
# macOS/Windows Docker Desktop: remove the /etc/machine-id volume from docker-compose.yml first
sudo docker compose up --build -d
sudo docker compose logs -f
```

#### Option C — Native Linux (no Docker)

Same `lib/cpu/` layout. Needs glibc **2.38+** (for example Ubuntu 24.04).

```
pip3 install -r requirements.txt
./run.sh
```

{% hint style="info" %}
Control routes (`/api/health`, `/api/machinecode`, `/api/activate`, `/api/licenseStatus`) return a JSON **envelope**. Process POSTs (`/api/detect`, `/api/quality`, …) return **engine JSON** as the HTTP body.
{% endhint %}

### License

Licenses are **offline**. [Request a License & Support](../../../../help/request-a-license-and-support.md).

### Try it

```bash
curl -s http://127.0.0.1:8083/api/health
IMG=$(base64 -w0 face.jpg)   # macOS: base64 -i face.jpg

curl -s -X POST http://127.0.0.1:8083/api/detect \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$IMG\"}"

curl -s -X POST http://127.0.0.1:8083/api/match \
  -H 'Content-Type: application/json' \
  -d "{\"image1\":\"$IMG\",\"image2\":\"$IMG\"}"
```

**Postman:** import `postman/FaceRecognition-API.postman_collection.json` from the repo. Base URL `http://127.0.0.1:8083`. Paths have **no** `/v1`.

**Gradio (host only):** the Docker image is API-only. On the host:

```
pip3 install -r requirements-demo.txt
DEMO_PORT=9003 API_BASE=http://127.0.0.1:8083 python3 demo.py
```

Open [http://127.0.0.1:9003](http://127.0.0.1:9003). Tabs: Detect, Quality, Match.

### Integrate into your own app

| Path                | When to use                                                                |
| ------------------- | -------------------------------------------------------------------------- |
| **HTTP** (`app.py`) | Any language. Keep this API running and `POST` base64 JPEGs.               |
| **`sdk.py`**        | Python on the **same** Linux host as `lib/cpu/` (or inside the container). |

You do **not** need Gradio in production.

### APIs

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

Python:

```python
import sdk

print(sdk.get_machine_code())
sdk.activate("license.txt")
sdk.init_sdk()
print(sdk.get_license_status())
print(sdk.detect(base64_image))
print(sdk.match(image1, image2))
```

Call order: `get_machine_code` → `activate` → `init_sdk` → detect / quality / feature / match / similarity.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Is this a face recognition API?**

Yes. The `faceplugin/face-recognition` Docker image serves a face recognition REST API on port 8083, with detect, match and similarity endpoints.

**How do I get the machine code for my license?**

Call `GET /api/machinecode`. The machine code inside Docker differs from the host's, so request it from the environment you will run.

**Does the Linux face recognition server support 1:N search?**

No. There is no `POST /api/identify` gallery. Store templates in your own database and compare them with `/api/similarity`.

**Can I run several containers with one license?**

Yes, on the same Linux host. Mount `/etc/machine-id` into each container so they share a machine code, then activate each with the same key.

**What Linux version do I need without Docker?**

Native Linux requires glibc 2.38 or later, for example Ubuntu 24.04.

### Related documentation

* [Face Recognition HTTP API](../../../../integration/http-api/face-recognition.md) · [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md)
* [Faceplugin Face Recognition Server SDK](./) · [Windows](face-recognition-windows-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Liveness Detection Linux SDK](../../liveness-detection-sdk/server-sdk/liveness-detection-linux-sdk.md)
* [ID Document Recognition Linux SDK](../../../id-document-recognition-sdk/recognition-and-liveness/server-sdk/id-document-recognition-linux-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Architecture](../../../../integration/deploy-and-host/architecture.md) · [SDK comparison](../../../../help/comparisons/)
