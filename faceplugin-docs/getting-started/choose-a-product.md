---
description: >-
  Choose a Faceplugin product and platform. Public GitHub repos, Docker images,
  ports, and how to combine Face Recognition, Liveness, and ID Document SDKs in
  an eKYC flow.
icon: compass
---

# Choose the Right Faceplugin SDK

Use this page as a catalog: **which GitHub repository to clone**, what the package or Docker image is called, and which HTTP port each server product listens on.

Always clone the **public** GitHub repository name shown in the tables below (for example `ID-Document-Recognition-Android`).

Need a vendor comparison? See [SDK comparison](../help/comparisons/).

### Which Faceplugin SDK Do I Need?

| You need                                      | Use                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Passport OCR / MRZ / ID card verification     | [ID Document capabilities](../products/id-document-recognition-sdk/capabilities.md) → [ID Document SDK](../products/id-document-recognition-sdk/) · [Recognition](../products/id-document-recognition-sdk/recognition.md)                                                                                                                                                                                                                                                           |
| Match faces 1:1 or 1:N / Face Recognition API | [Face SDK capabilities](../products/face-recognition-sdk/capabilities.md) → [Face SDK](../products/face-recognition-sdk/) · [Recognition](../products/face-recognition-sdk/recognition/)                                                                                                                                                                                                                                                                                            |
| Face anti-spoofing only                       | [Face SDK capabilities](../products/face-recognition-sdk/capabilities.md) → [Liveness](../products/face-recognition-sdk/liveness-detection-sdk/)                                                                                                                                                                                                                                                                                                                                    |
| Recognition and anti-spoofing together        | On a phone, use [Face Recognition + Liveness Mobile](../products/face-recognition-sdk/recognition-and-liveness/mobile-sdk/), which does both on the device. On a server, run [Face Recognition](../products/face-recognition-sdk/recognition/server-sdk/) on **8092** and [Face Liveness](../products/face-recognition-sdk/liveness-detection-sdk/server-sdk/) on **8093** as two services—never mix their runtime folders. |
| Document authenticity **without** OCR         | [ID Document Liveness SDK](../products/id-document-recognition-sdk/id-document-liveness-sdk/)                                                                                                                                                                                                                                                                                                                                                                                       |

### ID Document Recognition SDK: Passport, ID Card and Driver's License OCR

The **ID Document Recognition SDK** reads **passports, national ID cards and driver's licenses**, either on-device or through an HTTP API. It supports **16,900 document templates from 255 countries**.

**Document authenticity checks** require a license that includes that feature. The exception is the Document Liveness Linux product, which provides authenticity checks on its own.

Overview: [Document capabilities](../products/id-document-recognition-sdk/capabilities.md).

| Platform              | Clone                                                                                                                                                                                                                              | Package / image                | Port                        |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | --------------------------- |
| Android               | [ID-Document-Recognition-Android](https://github.com/Faceplugin-ltd/ID-Document-Recognition-Android)                                                                                                                               | `documentreadersdk.aar`        | —                           |
| iOS                   | [ID-Document-Recognition-iOS](https://github.com/Faceplugin-ltd/ID-Document-Recognition-iOS)                                                                                                                                       | `docsdk.framework`             | —                           |
| Flutter               | [ID-Document-Recognition-Flutter](https://github.com/Faceplugin-ltd/ID-Document-Recognition-Flutter)                                                                                                                               | `document_reader_sdk`          | —                           |
| React Native          | [ID-Document-Recognition-React-Native](https://github.com/Faceplugin-ltd/ID-Document-Recognition-React-Native)                                                                                                                     | `document-reader-sdk`          | —                           |
| Ionic Capacitor       | [ID-Document-Recognition-Ionic-Capacitor](https://github.com/Faceplugin-ltd/ID-Document-Recognition-Ionic-Capacitor)                                                                                                               | `document-reader-capacitor`    | —                           |
| Ionic Cordova         | [ID-Document-Recognition-Ionic-Cordova](https://github.com/Faceplugin-ltd/ID-Document-Recognition-Ionic-Cordova)                                                                                                                   | Cordova plugin                 | —                           |
| Windows               | [ID-Document-Recognition-Windows](https://github.com/Faceplugin-ltd/ID-Document-Recognition-Windows)                                                                                                                               | `sdk.py` + `app.py`            | API **8082**, demo **9002** |
| Linux / Docker        | [ID-Document-Recognition-Docker](https://github.com/Faceplugin-ltd/ID-Document-Recognition-Docker)                                                                                                                                 | `faceplugin/document-reader`   | API **8082**, demo **9002** |
| Node.js HTTP          | [ID-Document-Recognition-Node](https://github.com/Faceplugin-ltd/ID-Document-Recognition-Node)                                                                                                                                     | HTTP API (optional native lib) | API **8082**                |
| Go / C++ HTTP         | [ID-Document-Recognition-Go](https://github.com/Faceplugin-ltd/ID-Document-Recognition-Go), […-CPP](https://github.com/Faceplugin-ltd/ID-Document-Recognition-CPP)                                                                 | HTTP API                       | API **8082**                |
| JavaScript client     | [ID-Document-Recognition-JavaScript](https://github.com/Faceplugin-ltd/ID-Document-Recognition-JavaScript)                                                                                                                         | Browser + Node client          | → **8082**                  |
| React / Vue / Angular | […-React](https://github.com/Faceplugin-ltd/ID-Document-Recognition-React), […-Vue](https://github.com/Faceplugin-ltd/ID-Document-Recognition-Vue), […-Angular](https://github.com/Faceplugin-ltd/ID-Document-Recognition-Angular) | Web demos (HTTP)               | → **8082**                  |

### Face Recognition SDK

The **Face Recognition SDK** detects faces, reads face attributes, extracts face templates and performs **1:1 face matching**.

* **Mobile SDKs** also support **live 1:N face identification** (Identify, powered by VideoWorker) with a local person database.
* **Server APIs** currently work on still images and provide detection, quality, feature extraction, matching and similarity. There is no gallery-based 1:N endpoint on the server yet.

The **Face Recognition SDK** detects faces, reads face attributes, extracts face templates and performs **1:1 face matching**.

Capability overview: [Face SDK capabilities](../products/face-recognition-sdk/capabilities.md).

| Platform                                | Clone                                                                                                                  | Package / image                             | Port                                            |
| --------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- | ----------------------------------------------- |
| Android                                 | [FaceRecognition-LivenessDetection-Android](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Android)                                   | `facerecognitionsdk.aar`                    | —                                               |
| iOS                                     | [FaceRecognition-LivenessDetection-iOS](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-iOS)                                           | `facerecognitionsdk` + engine + onnxruntime | —                                               |
| Flutter                                 | [FaceRecognition-LivenessDetection-Flutter](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Flutter)                                   | `face_recognition_sdk`                      | —                                               |
| React Native                            | [FaceRecognition-LivenessDetection-ReactNative](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-ReactNative)                         | `face-recognition-sdk`                      | —                                               |
| Ionic Capacitor                         | [FaceRecognition-LivenessDetection-Ionic-Capacitor](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Ionic-Capacitor)                   | `face-recognition-capacitor`                | —                                               |
| Ionic Cordova                           | [FaceRecognition-LivenessDetection-Ionic-Cordova](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Ionic-Cordova)                       | Cordova plugin                              | —                                               |
| .NET MAUI                               | [FaceRecognition-LivenessDetection-DotNet-MAUI](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-DotNet-MAUI) | `FaceSdk` (C#)                     | —                                               |
| Windows (recognition)                   | [FaceRecognition-Windows](https://github.com/Faceplugin-ltd/FaceRecognition-Windows)                                   | `sdk.py` + `app.py`                         | API **8092**, demo **9092**                     |
| Docker (recognition)                    | [FaceRecognition-Docker](https://github.com/Faceplugin-ltd/FaceRecognition-Docker)                                     | `faceplugin/face-recognition` (`latest`, `latest-plus`) | API **8092**, demo **9092**         |

### Face Liveness Detection SDK: Anti-Spoofing

The **Face Liveness Detection SDK** is a standalone **presentation attack detection (PAD)** engine that stops photo, screen and other spoofing attempts.

* **Mobile SDKs** offer two modes. **Passive** scores a single camera frame with nothing asked of the user. **Active** first scores the face for spoofing (an anti-spoofing gate), then asks a face that clears the gate for a short shuffled sequence of challenges — blink, smile, turn left/right, look up/down, move closer — and returns the gate score together with every challenge outcome.
* **Server SDKs** are **passive only**: they return a liveness score for a single RGB JPEG image. There is no active liveness over HTTP.

The **Face Liveness Detection SDK** is a standalone **presentation attack detection (PAD)** engine that stops photo, screen and other spoofing attempts.

Capability overview: [Face SDK capabilities](../products/face-recognition-sdk/capabilities.md) (Liveness section).

| Platform        | Clone                                                                                                            | Package / image                                                                             | Port                        |
| --------------- | ---------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- | --------------------------- |
| Android         | [FaceLivenessDetection-Android](https://github.com/Faceplugin-ltd/FaceLivenessDetection-Android)                 | `facerecognitionsdk.aar`                                                                    | —                           |
| iOS             | [FaceLivenessDetection-iOS](https://github.com/Faceplugin-ltd/FaceLivenessDetection-iOS)                         | `facerecognitionsdk.framework` + `FacePluginSDK` / `FacePlugin` / `FaceDetect` xcframeworks | —                           |
| Flutter         | [FaceLivenessDetection-Flutter](https://github.com/Faceplugin-ltd/FaceLivenessDetection-Flutter)                 | `face_liveness_sdk`                                                                         | —                           |
| React Native    | [FaceLivenessDetection-ReactNative](https://github.com/Faceplugin-ltd/FaceLivenessDetection-ReactNative)         | `face-liveness-sdk`                                                                         | —                           |
| Ionic Capacitor | [FaceLivenessDetection-Ionic-Capacitor](https://github.com/Faceplugin-ltd/FaceLivenessDetection-Ionic-Capacitor) | `face-liveness-capacitor`                                                                   | —                           |
| Ionic Cordova   | [FaceLivenessDetection-Ionic-Cordova](https://github.com/Faceplugin-ltd/FaceLivenessDetection-Ionic-Cordova)     | `face-liveness-cordova`                                                                     | —                           |
| .NET MAUI       | [FaceLivenessDetection-DotNet-MAUI](https://github.com/Faceplugin-ltd/FaceLivenessDetection-DotNet-MAUI)         | MAUI app project                                                                            | —                           |
| Windows         | [FaceLivenessDetection-Windows](https://github.com/Faceplugin-ltd/FaceLivenessDetection-Windows)                 | `sdk.py` + `app.py`                                                                         | API **8093**, demo **9093** |
| Linux / Docker  | [FaceLivenessDetection-Docker](https://github.com/Faceplugin-ltd/FaceLivenessDetection-Docker)                   | `faceplugin/face-liveness`                                                                  | API **8093**, demo **9093** |

The mobile engine is the same build the Face Recognition product uses — `facerecognitionsdk.aar` with the public class `com.faceplugin.facerecognitionsdk.FaceRecognitionSDK` on Android, and `facerecognitionsdk.framework` plus the `FacePluginSDK.xcframework`, `FacePlugin.xcframework` (its `ios-arm64` slice is named `FacePPlugin.framework`) and `FaceDetect.xcframework` on iOS. Download it from the FaceRecognition-LivenessDetection-Android / -iOS GitHub releases.

{% hint style="info" %}
Face Recognition mobile apps already include **2D liveness on Identify**. Use the standalone Face Liveness product when you need anti-spoofing without enrollment / 1:N.
{% endhint %}

### ID Document Liveness SDK: Document Authenticity

The **ID Document Liveness SDK** checks **document authenticity and detects document spoofing** only. It has no OCR, MRZ or barcode API. If you need OCR and authenticity checks in one engine, use the Document Reader with a license that includes liveness.

| Platform       | Clone                                                                                                            | Image                          | Port                        |
| -------------- | ---------------------------------------------------------------------------------------------------------------- | ------------------------------ | --------------------------- |
| Linux / Docker | [ID-Document-Liveness-Detection-Docker](https://github.com/Faceplugin-ltd/ID-Document-Liveness-Detection-Docker) | `faceplugin/document-liveness` | API **8086**, demo **9006** |

### Combining SDKs for eKYC Identity Verification

**eKYC (electronic Know Your Customer)** is digital identity onboarding: a user scans an ID document, proves they are a live person and their selfie is matched to the document photo. Build this flow by combining the **Document Reader**, **Face Liveness** and **Face Recognition** SDKs in your app or backend.

Best practices for an on-premise eKYC deployment:

* **Run one process or container per product.** For example, run the ID Document service on port **8082**, Face Recognition on **8092**, and Face Liveness on **8093**.
* **Keep products separate.** Do not copy one product's runtime files into another product's `lib/cpu/` folder. Run each on its own server or container instead.
* **Plan your architecture:** see  [Architecture](../integration/deploy-and-host/architecture.md) for topology and which component handles what.
* **Size your servers:** see [Hosting requirements](../integration/deploy-and-host/hosting-requirements.md) for hardware sizing and Docker flags.

### Frequently Asked Questions

**Which Faceplugin SDK should I use for passport and ID card scanning?**

Use the ID Document Recognition SDK. It reads passports, national ID cards and driver's licenses, supports 16,900 templates from 255 countries and reads MRZ and barcodes.

**Can I run face recognition and face liveness detection in one server?**

On a phone, yes: the Recognition + Liveness mobile SDK does both on the device. On a server, run Face Recognition on **8092** and Face Liveness on **8093** as two services. You can also run them as two separate services on ports 8092 and 8093.

**Does the Face Recognition server API support 1:N identification?**

Not yet. The server APIs work on still images (detection, quality, feature extraction, match and similarity) and have no gallery-based 1:N endpoint. Live 1:N identification is available in the mobile SDKs.

**Does Faceplugin offer a complete eKYC app?**

No. Faceplugin provides the building blocks: you combine the ID Document, Face Liveness and Face Recognition SDKs in your own app or backend.

**What default ports do the Faceplugin server SDKs use?**

ID Document Recognition uses 8082, Face Recognition uses 8092, Face Liveness uses 8093 and ID Document Liveness uses 8086. Demo apps run on 9002, 9092, 9093 and 9006 respectively.

### Related documentation

* [Try it](try-it.md) · [HTTP API](../integration/http-api/) · [Deploy and host](../integration/deploy-and-host/)
* [FAQ](../help/resources/faq.md) · [Glossary](../help/resources/glossary.md) · [Troubleshooting](../help/resources/troubleshooting.md)
* [Request a License](../help/request-a-license-and-support.md)
