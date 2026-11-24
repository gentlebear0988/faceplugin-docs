---
description: >-
  Faceplugin Face Recognition API for Linux and Docker. On-premise detect,
  quality, templates, 1:1 verification and 1:N identification on port 8092.
  Offline after license activation.
icon: linux
---

# Docker

Fully on-premise **Face Recognition API for Docker and native Linux**. Image: `faceplugin/face-recognition`. Default port **8092**.

Two editions ship from the same image repository, and both expose the same API:

| Edition | Docker tag | What it is |
| ------- | ---------- | ---------- |
| **Lite** | `faceplugin/face-recognition:latest` | The standard recognition engine |
| **Plus** | `faceplugin/face-recognition:latest-plus` | The premium engine, for higher-accuracy deployments |

All processing stays on your server. **No** biometric data is sent to Faceplugin cloud.

Recognition and liveness do not ship as one server product. Run [Face Liveness Detection](../../liveness-detection-sdk/server-sdk/) as its own service when you need anti-spoofing on the server. On mobile, [Recognition + Liveness](../../recognition-and-liveness/mobile-sdk/) runs both in one SDK.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-Docker" %}

### Setup <a href="#setup" id="setup"></a>

Sizing: [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md).

{% stepper %}
{% step %}
#### Pull the image

```bash
# Lite edition
sudo docker pull faceplugin/face-recognition:latest

# or the Plus edition
sudo docker pull faceplugin/face-recognition:latest-plus

sudo docker run -d --name faceplugin-face-recognition \
  --shm-size=2gb --privileged \
  -p 8092:8092 \
  -v /etc/machine-id:/etc/machine-id:ro \
  faceplugin/face-recognition:latest
```

The runtime is inside the image, so there is nothing to download. On Docker Desktop (macOS/Windows) omit the `/etc/machine-id` volume.
{% endstep %}

{% step %}
#### Confirm health (no license yet)

```bash
curl -s http://127.0.0.1:8092/api/health
```
{% endstep %}

{% step %}
#### Copy the machine code

```bash
curl -s http://127.0.0.1:8092/api/machinecode
```

Send the machine code to Faceplugin. Docker and native host codes are **different**, so take it from the environment you will run in production.
{% endstep %}

{% step %}
#### Activate

Put your key in `license.txt`, then:

```bash
curl -s -X POST http://127.0.0.1:8092/api/activate \
  -H 'Content-Type: text/plain' \
  --data-binary @license.txt
```

A detached container does not re-read `license.txt` after it starts, so this call is required in Docker.
{% endstep %}
{% endstepper %}

### Run multiple containers with one license <a href="#run-multiple-containers" id="run-multiple-containers"></a>

On Linux, mount `/etc/machine-id` into each container so they share one machine code. Give each container its own name and host port:

```bash
sudo docker run -d --name faceplugin-face-recognition-2 \
  --shm-size=2gb --privileged \
  -p 8093:8092 \
  -v /etc/machine-id:/etc/machine-id:ro \
  faceplugin/face-recognition:latest
```

Activate each container with the same license key (`POST /api/activate` on each host port). On Docker Desktop (macOS/Windows), omit the volume; each container may then need its own license.

### Other ways to run

#### Option B — Docker Compose (local build)

```bash
git clone https://github.com/Faceplugin-ltd/FaceRecognition-Docker.git
cd FaceRecognition-Docker
```

Download the **Lite** or **Plus** Linux archive from the [GitHub release](https://github.com/Faceplugin-ltd/FaceRecognition-Docker/releases/tag/latest) and put the files **directly** in `lib/cpu/`, then:

```bash
# macOS/Windows Docker Desktop: remove the /etc/machine-id volume from docker-compose.yml first
sudo docker compose up --build -d
sudo docker compose logs -f
```

#### Option C — Native Linux (no Docker)

Same `lib/cpu/` layout from the same release. Needs glibc **2.38+** (for example Ubuntu 24.04).

```bash
pip3 install -r requirements.txt
./run.sh
```

{% hint style="info" %}
Control routes (`/api/health`, `/api/machinecode`, `/api/activate`, `/api/licenseStatus`) return a JSON **envelope**. Process POSTs (`/api/detect`, `/api/quality`, …) return **engine JSON** as the HTTP body.
{% endhint %}

### License

Licenses are **offline** and bound to the machine code of the environment that runs the server. [Request a License & Support](../../../../help/request-a-license-and-support.md).

### Try it

```bash
curl -s http://127.0.0.1:8092/api/health
IMG=$(base64 -w0 face.jpg)   # macOS: base64 -i face.jpg

curl -s -X POST http://127.0.0.1:8092/api/v1/detect \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$IMG\"}"

curl -s -X POST http://127.0.0.1:8092/api/v1/match \
  -H 'Content-Type: application/json' \
  -d "{\"image1\":\"$IMG\",\"image2\":\"$IMG\"}"
```

**Interactive docs:** open [http://127.0.0.1:8092/docs](http://127.0.0.1:8092/docs) while the API runs. The OpenAPI 3 description is at `/api/v1/openapi.json`.

**Postman:** import `postman/FaceRecognition-API.postman_collection.json` from the repo. Base URL `http://127.0.0.1:8092`.

**Gradio (host only):** the Docker image is API-only. On the host:

```bash
pip3 install -r requirements-demo.txt
DEMO_PORT=9092 API_BASE=http://127.0.0.1:8092 python3 demo.py
```

Open [http://127.0.0.1:9092](http://127.0.0.1:9092). Tabs: Face Detection, Quality Assessment, 1:1 Verification, 1:N Identification, Gallery and API.

### Gallery and 1:N identification

The gallery holds **persons**, and each person can hold several photos. It lives on the server in `data/` (SQLite plus face crops), so no biometric data leaves the machine.

```bash
# enroll
curl -s -X POST http://127.0.0.1:8092/api/v1/persons -F "image=@anna.jpg" -F "name=Anna Lee"

# 1:N identification of every face in a photo
curl -s -X POST http://127.0.0.1:8092/api/v1/identify -F "image=@probe.jpg" -F "top_k=5"
```

Enrollment rejects a duplicate face by default (code `-7`), and `check_quality=true` additionally rejects a low-quality face (code `-8`).

### Security

The API listens on **127.0.0.1** by default and sends no CORS headers, so out of the box it is reachable only from the machine it runs on.

| Variable | Purpose |
| -------- | ------- |
| `PORT` / `FACESDK_PORT` | Listen port (default **8092**). |
| `FACESDK_BIND_HOST` | Listen address (default `127.0.0.1`, this machine only). |
| `FACESDK_CORS_ORIGINS` | Comma-separated browser origins allowed to call the API (`*` for any). Default: none, server-to-server only. |
| `FACESDK_MAX_UPLOAD_MB` | Largest request body in MB (default 20). |
| `FACESDK_IMPORT_ROOT` | Folder that `POST /api/v1/persons/import` may read. Off when unset. |
| `FACESDK_THREADS` | Worker threads (default 8). |

{% hint style="warning" %}
The server has **no built-in authentication**. There is no API key and no bearer token: anything that can reach the port can call the API. If you bind it to an address other than loopback, put your own authentication in front of it — a reverse proxy, a service mesh, or a network policy — and do not expose it to the internet directly.
{% endhint %}

### Worked example

One client, start to finish: activate, enrol a few people, then identify a probe photo. Any language can do this — the API is plain HTTP, and the paths are the same on Windows and Docker.

```python
import requests

API = "http://127.0.0.1:8092/api/v1"

# 1. Activate once. The machine code comes from the machine that will run in production.
machine = requests.get(f"{API}/machinecode").json()
print("send this to FacePlugin:", machine)

with open("license.txt", "rb") as fh:
    requests.post(f"{API}/activate", data=fh.read(),
                  headers={"Content-Type": "text/plain"}).raise_for_status()

print(requests.get(f"{API}/licenseStatus").json())   # what the key unlocks

# 2. Enrol your people. One person can hold several photos; more photos identify better.
for name, path in [("Anna Lee", "anna.jpg"), ("Mina Ortiz", "mina.jpg")]:
    with open(path, "rb") as fh:
        r = requests.post(f"{API}/persons",
                          files={"image": fh},
                          data={"name": name, "check_quality": "true"})
    if r.status_code == 409:
        print(f"{name} already enrolled as", r.json())   # duplicate check, code -7
    elif r.status_code == 422:
        print(f"{name} rejected:", r.json())             # quality check, code -8
    else:
        r.raise_for_status()

# 3. Identify every face in a probe photo, 1:N against the gallery.
with open("probe.jpg", "rb") as fh:
    result = requests.post(f"{API}/identify",
                           files={"image": fh},
                           data={"top_k": 5}).json()

for face in result["faces"]:
    if face["status"] == "identified":
        best = face["candidates"][0]
        print(f"{best['name']} at {best['similarity']:.2f}")
    else:
        print(face["status"])          # no_match, or spoof when liveness is on

# 4. Or skip the gallery: keep templates yourself and compare them.
with open("a.jpg", "rb") as fa, open("b.jpg", "rb") as fb:
    match = requests.post(f"{API}/match", files={"image1": fa, "image2": fb}).json()
print(match)
```

**In-process instead of HTTP.** On the same machine, `sdk.py` skips the HTTP hop and takes base64 images:

```python
import sdk, gallery

print(sdk.get_machine_code())
sdk.activate("license.txt")
sdk.init_sdk()

gallery.enroll(base64_image, name="Anna Lee")
result = gallery.identify(base64_probe, top_k=5)
```

Every call returns `0` on success. The error codes are the same on both paths: `-1` bad input, `-2` no face, `-5` too large, `-6` more than one face, `-7` duplicate, `-8` low quality, `-10` not found.

### Integrate into your own app

| Path | When to use |
| ---- | ----------- |
| **HTTP** (`app.py`) | Any language. Keep this API running and send images to it. |
| **`sdk.py`** | Python on the **same** Linux host as `lib/cpu/`, or inside the container. |

You do **not** need Gradio in production.

### APIs

Every route lives under `/api/v1`, and also answers at the older path without `/v1`.

| Endpoint | Purpose |
| -------- | ------- |
| `GET /api/v1/health` | Process is listening (no license) |
| `GET /api/v1/machinecode` | Machine code |
| `GET /api/v1/licenseStatus` | License status |
| `GET /api/v1/backend` | `"cpu"` |
| `POST /api/v1/activate` | Activate and `init_sdk()` |
| `POST /api/v1/detect` | Detect faces, pose and attributes |
| `POST /api/v1/quality` | ICAO-style quality checks |
| `POST /api/v1/feature` | Extract a template |
| `POST /api/v1/match` | Compare two photos (1:1) |
| `POST /api/v1/similarity` | Compare two templates |
| `POST /api/v1/identify` | 1:N search of the gallery |
| `/api/v1/persons`, `/api/v1/persons/{id}`, `/api/v1/persons/import` | Enroll and manage people |
| `/api/v1/faces/{id}`, `/api/v1/faces/{id}/image` | Manage a person's photos |
| `PATCH /api/v1/settings` | Decision threshold and enrollment checks |

Full reference: [Face Recognition HTTP API](../../../../integration/http-api/face-recognition.md) · [Shared endpoints](../../../../integration/http-api/shared.md) · `/docs`.

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

Call order: `get_machine_code` → `activate` → `init_sdk` → detect / quality / feature / match / similarity. The gallery helpers live in `gallery.py`.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Is this a face recognition API?**

Yes. The `faceplugin/face-recognition` image serves a face recognition REST API on port 8092, with detect, quality, template, 1:1 match and 1:N identify endpoints.

**What is the difference between the Lite and Plus tags?**

`latest` is the Lite edition and `latest-plus` is the Plus edition. They expose the same API, so you can switch between them without changing your integration.

**How do I get the machine code for my license?**

Call `GET /api/machinecode`. The machine code inside Docker differs from the host's, so request it from the environment you will run.

**Does the server support 1:N search?**

Yes. `POST /api/v1/identify` searches the gallery you enrolled through `/api/v1/persons`. You can still keep templates in your own database and compare them with `/api/v1/similarity`.

**Can I run several containers with one license?**

Yes, on the same Linux host. Mount `/etc/machine-id` into each container so they share a machine code, then activate each with the same key.

**What Linux version do I need without Docker?**

Native Linux requires glibc 2.38 or later, for example Ubuntu 24.04.

### Related documentation

* [Face Recognition HTTP API](../../../../integration/http-api/face-recognition.md) · [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md)
* [Faceplugin Face Recognition Server SDK](./) · [Windows](face-recognition-windows-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Liveness Detection Server SDK](../../liveness-detection-sdk/server-sdk/)
* [ID Document Recognition Linux SDK](../../../id-document-recognition-sdk/recognition-and-liveness/server-sdk/id-document-recognition-linux-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Architecture](../../../../integration/deploy-and-host/architecture.md) · [SDK comparison](../../../../help/comparisons/)
