---
description: >-
  Faceplugin Face Recognition SDK Linux Docker — recognition and face liveness
  in one container. faceplugin/face-recognition-liveness-sdk on port 8083.
  detect, match, liveness.
icon: linux
---

# Linux

Fully on-premise **Face Recognition + Face Liveness API for Linux / Docker** in **one** container. Image: `faceplugin/face-recognition-liveness-sdk`. Default port **8083**. Gradio **9003**.

One wrapper (`libFaceRecognitionSDK.so`), one license, two model packs (`far.fpk` + `fal.fpk`). Your license can unlock Recognition only, Liveness only, or both.

This is **not** the recognition-only [Face Recognition Linux SDK](../../recognition/server-sdk/face-recognition-linux-sdk.md) (`faceplugin/face-recognition`). It is **not** the anti-spoofing-only [Face Liveness Detection Linux SDK](../../liveness-detection-sdk/server-sdk/liveness-detection-linux-sdk.md) (port **8084**). Do not copy two products’ Google Drive runtimes into one `lib/cpu/` folder.

All processing stays on your server. **No** biometric data is sent to Faceplugin cloud.

### Code <a href="#code" id="code"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Docker" %}

### Setup <a href="#setup" id="setup"></a>

Sizing: [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md).

{% stepper %}
{% step %}
#### Pull and run the Docker image

```
sudo docker pull faceplugin/face-recognition-liveness-sdk:latest
sudo docker run -d --name faceplugin-face-recognition-liveness-sdk \
  --shm-size=2gb --privileged \
  -p 8083:8083 \
  -v /etc/machine-id:/etc/machine-id:ro \
  faceplugin/face-recognition-liveness-sdk:latest
```

On Docker Desktop (macOS/Windows) omit the `/etc/machine-id` volume. Port **8083** is shared with recognition-only — run **one** Face Recognition container at a time.
{% endstep %}

{% step %}
#### Check that the API is running

```
curl -s http://127.0.0.1:8083/api/health
```
{% endstep %}

{% step %}
#### Get your machine code

```
curl -s http://127.0.0.1:8083/api/machinecode
```

Send machine code to Faceplugin. Docker and host machine codes differ.
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

### Run multiple containers with one license <a href="#run-multiple-containers" id="run-multiple-containers"></a>

You only need this section if you want to run multiple Face Recognition + Liveness containers on the same Linux host.

On Linux, mount `/etc/machine-id` into each container so they use the same machine code. Each container must have a different container name and host port.

For example:

```bash
sudo docker run -d --name faceplugin-face-recognition-liveness-sdk-2 \
  --shm-size=2gb --privileged \
  -p 8085:8083 \
  -v /etc/machine-id:/etc/machine-id:ro \
  faceplugin/face-recognition-liveness-sdk:latest
```

Activate each container with the same license key (`POST /api/activate` on each host port).

On Docker Desktop (macOS/Windows), omit the `/etc/machine-id` volume. Each container may require its own license.

### Other ways to run

Most teams use Docker Hub (above). These options are for teams that need a local image build or a native host process.

#### Option B — Docker Compose (local build)

1. Clone the repo and download the Google Drive runtime into `./lib/cpu/` (files directly in that folder, not a nested zip folder):

```
git clone https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Docker.git
cd FaceRecognition-LivenessDetection-Docker
```

Drive folder: [FaceRecognition-LivenessDetection-Docker runtime](https://drive.google.com/drive/folders/1Lzz3eb_JMDZ0xyGtnGzxsUmMbgaYzin6). Confirm:

```
ls lib/cpu/libFaceRecognitionSDK.so lib/cpu/libfar-eng.so lib/cpu/far.fpk lib/cpu/libfal-eng.so lib/cpu/fal.fpk
```

2. Build and start:

```
# macOS/Windows Docker Desktop: remove the /etc/machine-id volume from docker-compose.yml first
sudo docker compose up --build -d
sudo docker compose logs -f
```

Detached Compose has no TTY, so activate with `POST /api/activate` after you receive a license key.

#### Option C — Native Linux (no Docker)

Requires the same `./lib/cpu/` layout as Option B. Needs glibc **2.38+** (for example Ubuntu 24.04).

```
cd FaceRecognition-LivenessDetection-Docker
pip3 install -r requirements.txt
./run.sh
```

Copy the machine code from the terminal, then activate with `POST /api/activate` or paste the license key when the process prompts you.

{% hint style="info" %}
Control routes (`/api/health`, `/api/machinecode`, `/api/activate`, `/api/licenseStatus`) return a JSON **envelope**. Process POSTs return **engine JSON** as the HTTP body. Native `./run.sh` sets `LD_PRELOAD` for liveness VFS hooks — Docker images already do this.
{% endhint %}

### APIs

Same recognition routes as [Face Recognition Linux SDK](../../recognition/server-sdk/face-recognition-linux-sdk.md), **plus** liveness:

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

Some routes wrap the result in a small JSON object (an **envelope**). Process POSTs return **engine JSON** as the body. There is **no** `POST /api/identify` (no server-side 1:N gallery).

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

Score **≥ 0.5** → Real / pass (same threshold family as Face Liveness on **8084**).

Local Gradio: `DEMO_PORT=9003 API_BASE=http://127.0.0.1:8083 python3 demo.py` (Detect, Quality, Match, **Liveness**).

### Frequently Asked Questions

**Is this one container for both face recognition and liveness?**

Yes. The `faceplugin/face-recognition-liveness-sdk` image serves both on port 8083, including `POST /api/liveness`.

**Can I run recognition and liveness as separate services instead?**

Yes. Use the Face Recognition Linux SDK on port 8083 and the Face Liveness Linux SDK on port 8084.

**What liveness score counts as a real face?**

A score of 0.5 or higher counts as Real, the same threshold as the standalone Face Liveness product.

**Does one license cover both recognition and liveness?**

The package uses one license, which can enable recognition only, liveness only, or both.

**Does the combined server support 1:N face search?**

No. There is no `POST /api/identify`. Store templates in your own database and compare them with `/api/similarity`.

**Is there a combined package for Windows?**

Yes. See the [Face Recognition + Liveness Windows SDK](face-recognition-sdk-windows.md).

### Related documentation

* [Face Recognition Linux SDK](../../recognition/server-sdk/face-recognition-linux-sdk.md) (recognition only) · [Glossary](../../../../help/resources/glossary.md)
* [Face Liveness Detection Linux SDK](../../liveness-detection-sdk/server-sdk/liveness-detection-linux-sdk.md)
* [Face Recognition Server SDK](../../recognition/server-sdk/) · [Choose a product](../../../../getting-started/choose-a-product.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
