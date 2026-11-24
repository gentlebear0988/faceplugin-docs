---
description: >-
  Faceplugin Face Recognition API for Windows. On-premise detect, quality,
  templates, 1:1 verification and 1:N identification on port 8092, with Python
  bindings.
icon: windows
---

# Windows

Fully on-premise **commercial Face Recognition HTTP API for Windows**, with Python bindings. Native x64, **CPU only**, default port **8092**.

This is not the [Open Source Face Recognition Windows SDK](../open-source-windows-and-linux/open-source-face-recognition-windows-sdk.md), which is a smaller, lower-accuracy sample.

Two editions ship on the release page, and both expose the same API and the same `lib\cpu\` layout:

| Edition | What it is |
| ------- | ---------- |
| **Lite** | The standard recognition engine |
| **Plus** | The premium engine, for higher-accuracy deployments |

All processing stays on your machine. **No** biometric data is sent to Faceplugin cloud.

Recognition and liveness do not ship as one server product. Run [Face Liveness Detection for Windows](../../liveness-detection-sdk/server-sdk/liveness-detection-windows-sdk.md) as its own service when you need anti-spoofing on the server.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-Windows" %}

### Setup <a href="#setup" id="setup"></a>

Sizing: [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md).

{% stepper %}
{% step %}
#### Get the runtime

`lib\cpu\` is empty on GitHub because the binaries and model packs are too large. Download the **Lite** or **Plus** Windows archive from the [GitHub release](https://github.com/Faceplugin-ltd/FaceRecognition-Windows/releases/tag/latest) and extract every file **directly** into `lib\cpu\`:

```
FaceRecognition-Windows/
└── lib/
    └── cpu/
        ├── FaceRecognitionSDK.dll
        ├── facerec.dll
        ├── onnxruntime.dll
        ├── far.fpk
        └── ... (OpenCV, OpenSSL and VC++ runtime DLLs)
```

A nested folder breaks the run. The VC++ runtime DLLs ship inside `lib\cpu\`, so you do not need `vcredist`.
{% endstep %}

{% step %}
#### Start the API

```batch
pip install -r requirements.txt
run.bat
```

`run.bat` puts `lib\cpu` on `PATH` and starts the API on **8092**. It starts without a license, and prints your machine code.
{% endstep %}

{% step %}
#### Copy the machine code

```batch
curl -s http://127.0.0.1:8092/api/machinecode
```

Send it to Faceplugin. **Windows machine codes are unique to each PC**, so take it from the machine you will deploy on.
{% endstep %}

{% step %}
#### Activate

Paste the key at the `run.bat` prompt, or put it in `license.txt` and call:

```batch
curl -s -X POST http://127.0.0.1:8092/api/activate -H "Content-Type: text/plain" --data-binary @license.txt
```
{% endstep %}
{% endstepper %}

### License

Licenses are **offline** and bound to the machine. The offline cryptography is built into the SDK, so no OpenSSL install is needed. [Request a License & Support](../../../../help/request-a-license-and-support.md).

### Try it

```batch
curl -s http://127.0.0.1:8092/api/health

curl -s -X POST http://127.0.0.1:8092/api/v1/detect -F "image=@face.jpg"
curl -s -X POST http://127.0.0.1:8092/api/v1/match -F "image1=@a.jpg" -F "image2=@b.jpg"
```

**Interactive docs:** open [http://127.0.0.1:8092/docs](http://127.0.0.1:8092/docs) while the API runs. The OpenAPI 3 description is at `/api/v1/openapi.json`.

**Postman:** import `postman/FaceRecognition-API.postman_collection.json` from the repo. Base URL `http://127.0.0.1:8092`.

**Gradio (host only):**

```batch
pip install -r requirements-demo.txt
run_demo.bat
```

Open [http://127.0.0.1:9092](http://127.0.0.1:9092). Tabs: Face Detection, Quality Assessment, 1:1 Verification, 1:N Identification, Gallery and API.

| Tab | What it shows |
| --- | ------------- |
| **Face Detection** | Faces, pose and attributes, with a result table and raw JSON |
| **Quality Assessment** | ICAO-style face image quality checks |
| **1:1 Verification** | Two photos, a match decision against the threshold, and the similarity of every face pair |
| **1:N Identification** | A ranked candidate list from the local gallery, with timing |
| **Gallery** | Enroll from files or a folder, search, rename, add or remove photos, find duplicates |
| **API** | Endpoint list, curl examples, and a link to the Swagger docs |

### Gallery and 1:N identification

The gallery holds **persons**, each with one or more photos, in `data\` on this machine (SQLite plus face crops).

```batch
curl -s -X POST http://127.0.0.1:8092/api/v1/persons -F "image=@anna.jpg" -F "name=Anna Lee"
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
| **HTTP** (`app.py` via `run.bat`) | Any language. Keep this API running and send images to it. |
| **`sdk.py`** | Python on the **same** Windows machine as `lib\cpu\`. No HTTP hop. |

Copy `sdk.py` and `lib\cpu\` into your project, put `lib\cpu` on `PATH`, then call `get_machine_code` → `activate` → `init_sdk` → detect / quality / feature / match / similarity. The gallery helpers live in `gallery.py`. You do not need Gradio in production.

```python
import sdk

print(sdk.get_machine_code())
sdk.activate("license.txt")
sdk.init_sdk()
print(sdk.get_license_status())
print(sdk.detect(base64_image))
```

### APIs

Every route lives under `/api/v1`, and also answers at the older path without `/v1`: `health`, `machinecode`, `licenseStatus`, `backend`, `activate`, `detect`, `quality`, `feature`, `match`, `similarity`, `identify`, `similar`, `settings`, `persons`, `persons/{id}`, `persons/import`, `faces/{id}`, `faces/{id}/image`.

Full reference: [Face Recognition HTTP API](../../../../integration/http-api/face-recognition.md) · [Shared endpoints](../../../../integration/http-api/shared.md) · `/docs`.

### Frequently Asked Questions

**Is there a Docker image for Windows?**

No. Windows runs natively through `run.bat`. For containers, use [Face Recognition Docker](face-recognition-linux-sdk.md).

**What is the difference between Lite and Plus?**

Both editions expose the same API and install into `lib\cpu\`. Lite is the standard engine; Plus is the premium engine for higher-accuracy deployments.

**Does the Windows server support 1:N search?**

Yes. `POST /api/v1/identify` searches the local gallery you enrolled through `/api/v1/persons`.

**Does it need a GPU?**

No. This product is CPU only; there is no `gpu\` package.

**Where does the machine code come from?**

`GET /api/machinecode`, or the `run.bat` log. Each Windows PC has its own code.

### Related documentation

* [Face Recognition HTTP API](../../../../integration/http-api/face-recognition.md) · [Hosting requirements](../../../../integration/deploy-and-host/hosting-requirements.md)
* [Faceplugin Face Recognition Server SDK](./) · [Docker](face-recognition-linux-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Liveness Detection Windows SDK](../../liveness-detection-sdk/server-sdk/liveness-detection-windows-sdk.md)
* [ID Document Recognition Windows SDK](../../../id-document-recognition-sdk/recognition-and-liveness/server-sdk/id-document-recognition-windows-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Architecture](../../../../integration/deploy-and-host/architecture.md) · [SDK comparison](../../../../help/comparisons/)
