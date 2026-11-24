---
description: >-
  This quick start shows you how to test Faceplugin's face recognition, face
  liveness detection and ID document recognition SDKs with copy-paste commands.
icon: flask
---

# Try Faceplugin SDKs

There is no need to build a UI first: check that the server is running, activate your license once, then call the main API for each product.

{% hint style="info" %}
**Fastest way to test:** use the server SDKs. You only need Docker Hub and `curl`, with no Android Studio required. The mobile snippets in step 4 assume you have already built and run the demo app.
{% endhint %}

### Default API Ports

| Server SDK              | Default port |
| ----------------------- | ------------ |
| ID Document Recognition | 8082         |
| Face Recognition        | 8092         |
| Face Liveness           | 8084         |
| ID Document Liveness    | 8086         |

### 1. Check the Server and Get Your Machine Code (No License Needed)

Before you have a license, you can confirm the server is running and get the **machine code** you need to request a license. Call the `health` and `machinecode` endpoints. The example below uses the Face Recognition server on port 8092; change the port for other products.

```bash
curl -s http://127.0.0.1:8092/api/health
curl -s http://127.0.0.1:8092/api/machinecode
```

Send the machine code with your [license request](../help/request-a-license-and-support.md).

### 2. Activate Your License (One Time)

Save your license key to a file, then POST it to `/api/activate`. A successful call returns **"Successfully activated"** and loads the engine. You only need to do this once per server.

```bash
# license.txt contains one line: license key
curl -s -X POST http://127.0.0.1:8092/api/activate \
  -H 'Content-Type: text/plain' \
  --data-binary @license.txt
```

### 3. Test the Main Server APIs

{% tabs %}
{% tab title="Face Recognition" %}
```bash
IMG=$(base64 -w0 face.jpg)   # macOS: base64 -i face.jpg

curl -s -X POST http://127.0.0.1:8092/api/detect \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$IMG\"}"

curl -s -X POST http://127.0.0.1:8092/api/match \
  -H 'Content-Type: application/json' \
  -d "{\"image1\":\"$IMG\",\"image2\":\"$IMG\"}"
```

Install: [Face Recognition Docker SDK](../products/face-recognition-sdk/recognition/server-sdk/face-recognition-linux-sdk.md) or [Windows](../products/face-recognition-sdk/recognition/server-sdk/face-recognition-windows-sdk.md). For anti-spoofing on the server, add [Face Liveness](../products/face-recognition-sdk/liveness-detection-sdk/server-sdk/) on port **8084**; on a phone, [Recognition + Liveness Mobile](../products/face-recognition-sdk/recognition-and-liveness/mobile-sdk/) does both on the device.
{% endtab %}

{% tab title="Face Liveness" %}
```bash
IMG=$(base64 -w0 face.jpg)

curl -s -X POST http://127.0.0.1:8084/api/liveness \
  -H 'Content-Type: application/json' \
  -d "{\"image\":\"$IMG\"}"
```

Score **≥ 0.5** is Real / pass. JPEG only. [Face Liveness Detection Linux SDK](../products/face-recognition-sdk/liveness-detection-sdk/server-sdk/liveness-detection-linux-sdk.md). Face Liveness runs as its own service on **8084**, beside Face Recognition on **8092**.
{% endtab %}

{% tab title="ID Document" %}
```bash
FRONT=$(base64 -w0 passport_front.jpg)

curl -s -X POST http://127.0.0.1:8082/api/documentRecognition \
  -H 'Content-Type: application/json' \
  -d "{\"images\":[{\"image\":\"$FRONT\",\"page_idx\":0}]}"
```

Combined pipeline: `POST /api/documentProcess`. Dedicated Document Liveness product: port **8086**. [ID Document Recognition Linux SDK](../products/id-document-recognition-sdk/recognition-and-liveness/server-sdk/id-document-recognition-linux-sdk.md).
{% endtab %}
{% endtabs %}

Windows: use Git Bash or WSL for `base64`, or Python:

```python
import base64, json, urllib.request
img = base64.b64encode(open("face.jpg", "rb").read()).decode()
req = urllib.request.Request(
    "http://127.0.0.1:8092/api/detect",
    data=json.dumps({"image": img}).encode(),
    headers={"Content-Type": "application/json"},
)
print(urllib.request.urlopen(req).read().decode())
```

### 4. Test the Mobile SDKs (Android and Flutter)

Once the demo app compiles and runs, use these snippets to call the SDK from your own code. Each one gets the machine code, activates the license, initializes the engine and then runs recognition.

* **Run SDK calls on a background thread** (or in an async function in Flutter) so the UI does not freeze.
* **Keep the demo's application ID** so the included demo license keeps working.

{% tabs %}
{% tab title="Android Face Recognition" %}
```kotlin
Thread {
    FaceRecognitionSDK.getMachineCode(context)
    var code = FaceRecognitionSDK.setActivation(context, "license key") // demo key from the repo
    if (code == FaceRecognitionSDK.SDK_SUCCESS) {
        code = FaceRecognitionSDK.init(context)
    }
    val faces = FaceRecognitionSDK.faceDetection(bitmap, FaceDetectionParam())
    val template = FaceRecognitionSDK.templateExtraction(bitmap, faces[0])
    val score = FaceRecognitionSDK.similarity(template, template)
}.start()
```

Install: [Face Recognition Android SDK](../products/face-recognition-sdk/recognition-and-liveness/mobile-sdk/face-recognition-android-sdk.md).
{% endtab %}

{% tab title="Flutter Face Recognition" %}
```dart
import 'package:face_recognition_sdk/face_recognition_sdk.dart';

Future<void> tryOnce(String imagePath, String license) async {
  await getMachineCode();
  if (await setActivation(license) != sdkSuccess) return;
  if (await init() != sdkSuccess) return;
  final faces = await faceDetection(imagePath);
  final t1 = await templateExtraction(imagePath, faces.first);
  final t2 = await templateExtraction(imagePath, faces.first);
  final score = await similarity(t1, t2);
}
```

Install: [Face Recognition Flutter SDK](../products/face-recognition-sdk/recognition-and-liveness/mobile-sdk/face-recognition-flutter-sdk.md).
{% endtab %}

{% tab title="Android ID Document" %}
```kotlin
Thread {
    DocumentReaderSDK.getMachineCode(context)
    var code = DocumentReaderSDK.setActivation(context, "license key")
    if (code == DocumentReaderSDK.SDK_SUCCESS) {
        code = DocumentReaderSDK.init(context)
    }
    val json = DocumentReaderSDK.recognize(frontBitmap, null, "none")
}.start()
```

`locateDocument` is overlay-only (no OCR). Install: [ID Document Recognition Android SDK](../products/id-document-recognition-sdk/recognition-and-liveness/mobile-sdk/id-document-recognition-android-sdk.md).
{% endtab %}
{% endtabs %}

### Frequently Asked Questions

**What is the fastest way to test a Faceplugin SDK?**

Use a server SDK. Pull the Docker image, then call the API with `curl`. You don't need Android Studio or a UI.

**How do I get the machine code for a Faceplugin license?**

Call `/api/machinecode` on the running server, then send the result with your license request.

**How do I activate a Faceplugin server license?**

POST your license key to `/api/activate`. The server returns "Successfully activated" and loads the engine. This only needs to be done once.

**What liveness score means a real face?**

On the Face Liveness API, a score of 0.5 or higher means the face is real.

**Which ports do the Faceplugin server APIs use?**

ID Document Recognition uses 8082, Face Recognition 8092, Face Liveness 8084 and ID Document Liveness 8086.

### Related documentation

* [HTTP API](../integration/http-api/) · [Deploy and host](../integration/deploy-and-host/) · [Choose a product](choose-a-product.md)
* [Glossary](../help/resources/glossary.md) · [Status codes](../help/resources/status-codes.md) · [Troubleshooting](../help/resources/troubleshooting.md)
* [Request a License](../help/request-a-license-and-support.md)
