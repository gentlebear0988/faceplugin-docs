---
description: >-
  Faceplugin Face Recognition Android SDK. Fully on-premise face matching, 1:N
  identify, and 2D liveness. AAR, setActivation, init, faceDetection,
  VideoWorker.
icon: android
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
  actions:
    visible: true
  anchors:
    visible: true
---

# Android

Fully on-premise **Face Recognition SDK for Android**. Detect faces, extract templates you store yourself, match 1:1 or live 1:N, and check passive 2D liveness on the device. Images never leave the phone.

Public class: `com.faceplugin.facerecognitionsdk.FaceRecognitionSDK`. Demo tiles: Enroll, Identify, Capture, Attribute, Settings, About.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-Android" %}

### Setup <a href="#setup" id="setup"></a>

1. Copy the SDK (`libfacesdk` folder) to the `root` folder in your project. Place `facerecognitionsdk.aar` from [Google Drive](https://drive.google.com/drive/folders/1kpzYVv9Gbm_pEpDe9-x7FGB4NWZzvez0) inside `libfacesdk/` (next to `libfacesdk/build.gradle`).
2. Add SDK to the project in `settings.gradle`

```
rootProject.name = "YourProjectName"
include ':app'
include ':libfacesdk'
```

3. Add dependency to your `app/build.gradle`

```
android {
    defaultConfig {
        minSdk 24
        ndk { abiFilters 'arm64-v8a', 'armeabi-v7a' }
    }
    packaging {
        jniLibs { useLegacyPackaging = true }
    }
}
dependencies {
    implementation project(path: ':libfacesdk')
}
```

4. Add camera / gallery permissions in `AndroidManifest.xml`. Call **setActivation → init** on a **background** thread. Keep the demo package name **`com.faceplugin.facerecognitionsdk`**. Request a new license key for your own app.

{% hint style="info" %}
Serialize native calls on one thread. The engine is not concurrent. First `init` unpacks on-device models (a few seconds).
{% endhint %}

Status codes: **0** Success (`SDK_SUCCESS`), **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

### APIs

#### <mark style="color:orange;">setActivation:</mark> This API is used to activate the SDK <a href="#setactivation" id="setactivation"></a>

```java
public static int setActivation(Context context, String license);
```

| **Input**        | <ul><li><strong>context</strong> (Context): Android <code>Context</code></li><li><strong>license</strong> (String): The license string (<code>license key</code>)</li></ul> |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Return value** | <p>The SDK activation status code.</p><ul><li>0: Success</li><li>1: Invalid license</li><li>2: Expired</li><li>3: Not activated</li><li>4: Init failed</li></ul>            |

#### <mark style="color:orange;">init:</mark> This API is used to initialize the SDK <a href="#init" id="init"></a>

```java
public static int init(Context context) throws IOException;
```

| **Input**        | <ul><li><strong>context</strong> (Context): Android <code>Context</code></li></ul>                                 |
| ---------------- | ------------------------------------------------------------------------------------------------------------------ |
| **Return value** | <p>The SDK initialization status code.</p><ul><li>0: Success</li><li>1–4: see <code>setActivation</code></li></ul> |

#### <mark style="color:orange;">getMachineCode:</mark> This API is used to retrieve the machine code <a href="#getmachinecode" id="getmachinecode"></a>

```java
public static String getMachineCode(Context context);
```

| **Input**        | <ul><li><strong>context</strong> (Context): Android <code>Context</code></li></ul> |
| ---------------- | ---------------------------------------------------------------------------------- |
| **Return value** | Machine code string (for license requests).                                        |

#### <mark style="color:orange;">getLicenseStatus:</mark> This API is used to read license status <a href="#getlicensestatus" id="getlicensestatus"></a>

```java
public static String getLicenseStatus();
public static boolean allowsRecognition();
public static boolean allowsLiveness();
public static String lastLicenseError();
public static boolean isActivated();
```

| **Input**        | None                |
| ---------------- | ------------------- |
| **Return value** | JSON status string. |

#### <mark style="color:orange;">faceDetection:</mark> This API is used to detect faces <a href="#facedetection" id="facedetection"></a>

```java
public static List<FaceBox> faceDetection(Bitmap bitmap, FaceDetectionParam param);
```

| **Input**        | <ul><li><strong>bitmap</strong> (Bitmap): The Bitmap image</li><li><strong>param</strong> (<strong>FaceDetectionParam</strong>): Parameters for face detection</li></ul> |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Return value** | A list of **FaceBox** objects representing the detected faces.                                                                                                           |

**FaceDetectionParam**

```java
public class FaceDetectionParam {
    public boolean check_liveness = false;
    public int check_liveness_level = 0; // 0: High Accuracy, 1: Light
    public boolean check_eye_closeness = false;
    public boolean check_face_occlusion = false;
    public boolean estimate_age_gender = false;
    public boolean check_pose = true;
    public boolean check_landmarks = true;
    public boolean check_quality = false;
    public boolean check_emotion = false;
    public boolean check_mask = false;
    public boolean check_glasses = false;
}
```

Use `FaceDetectionParam.allAttributes()` when you want liveness, age, gender, emotion, and quality in one call.

**FaceBox**

```java
public class FaceBox {
    public int x1, y1, x2, y2;
    public float yaw, roll, pitch, liveness;
    public float face_quality, face_luminance;
    public float left_eye_closed, right_eye_closed, face_occlusion, mouth_opened;
    public int age, gender, landmarkCount;
    public float[] landmarks_68;
    public String livenessLabel, genderLabel, emotionLabel, maskLabel;
    public String qualityLabel, eyesLeftLabel, eyesRightLabel, rawJson;
}
```

The liveness score ranges from **0.0** to **1.0**. If it is greater than the threshold, it is a real face. Demo default threshold is **0.5**.

#### <mark style="color:orange;">templateExtraction:</mark> This API is used to extract face template <a href="#templateextraction" id="templateextraction"></a>

```java
public static byte[] templateExtraction(Bitmap bitmap, FaceBox faceBox);
```

| **Input**        | <ul><li><strong>bitmap</strong> (Bitmap): The Bitmap image</li><li><strong>faceBox</strong> (<strong>FaceBox</strong>): The bounding box of the detected face</li></ul> |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Return value** | A byte array representing the extracted template from the face. Store these in **your** database.                                                                       |

#### <mark style="color:orange;">similarityCalculation:</mark> This API is used to calculate the similarity between two face templates <a href="#similaritycalculation" id="similaritycalculation"></a>

```java
public static float similarityCalculation(byte[] feature1, byte[] feature2);
```

| **Input**        | <ul><li><strong>feature1</strong> (byte[]): The first face template</li><li><strong>feature2</strong> (byte[]): The second face template</li></ul> |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Return value** | A float similarity score. Demo Identify default threshold is **0.67**.                                                                             |

`similarity(byte[] feature1, byte[] feature2)` is an alias of `similarityCalculation`.

#### <mark style="color:orange;">detect:</mark> This API is used to detect faces and return engine JSON <a href="#detect" id="detect"></a>

```java
public static String detect(Bitmap bitmap, boolean crop);
public static String detect(Bitmap bitmap, boolean crop, int flags);
```

| **Input**        | <ul><li><strong>bitmap</strong> (Bitmap): The Bitmap image</li><li><strong>crop</strong> (boolean): Whether to crop faces</li><li><strong>flags</strong> (int): Bit flags (<code>DETECT_ALL</code>, <code>DETECT_LANDMARKS</code>, <code>DETECT_LIVENESS</code>, <code>DETECT_LIVENESS_ACCURATE</code>, …)</li></ul> |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Return value** | JSON string (same idea as HTTP `POST /api/detect`).                                                                                                                                                                                                                                                                  |

#### <mark style="color:orange;">quality:</mark> This API is used to run face image quality checks <a href="#quality" id="quality"></a>

```java
public static String quality(Bitmap bitmap, boolean crop);
```

#### <mark style="color:orange;">match:</mark> This API is used to compare two photos (1:1) <a href="#match" id="match"></a>

```java
public static String match(Bitmap image1, Bitmap image2, boolean crop);
```

#### <mark style="color:orange;">extractFeature:</mark> This API is used to extract a template from a cropped face image <a href="#extractfeature" id="extractfeature"></a>

```java
public static String extractFeature(Bitmap bitmap);
```

Also: `cropFace(Bitmap, FaceBox)`, `parseFeatureBytes(String json)`, `yuv2Bitmap(...)`, `estimatorStatusJSON()`.

#### <mark style="color:orange;">setLandmarkMode:</mark> This API is used to set the landmark model <a href="#setlandmarkmode" id="setlandmarkmode"></a>

```java
public static int setLandmarkMode(int mode); // 14, 68, or 468
public static int getLandmarkMode();
```

#### <mark style="color:orange;">VideoWorker:</mark> This API is used for live 1:N identify <a href="#videoworker" id="videoworker"></a>

Call in this order: `startVideoWorker` → `syncVideoWorkerDatabase(templates)` → `addVideoWorkerFrame` → `stopVideoWorker`. Listen with `setVideoWorkerEventHandler`. Identify in the demo is **passive 2D** liveness plus VideoWorker.

```java
FaceRecognitionSDK.startVideoWorker(config);
FaceRecognitionSDK.syncVideoWorkerDatabase(templates, 0.67f);
FaceRecognitionSDK.addVideoWorkerFrame(bitmap);
FaceRecognitionSDK.stopVideoWorker();
```

Also: `startVideoWorker(float matchThreshold)`, `addVideoWorkerFrameRgb`, `addVideoWorkerFrameBgra`, `VideoWorkerConfig` / `ActiveLivenessConfig`.

| **Input**        | Match threshold, enrolled templates, and camera frames. |
| ---------------- | ------------------------------------------------------- |
| **Return value** | Match events via the VideoWorker listener (JSON).       |

#### <mark style="color:orange;">deinit:</mark> This API is used to unload the engine <a href="#deinit" id="deinit"></a>

```java
public static int deinit();
```

| **Input**        | None                         |
| ---------------- | ---------------------------- |
| **Return value** | Status code. **0**: Success. |

### Run the demo

1. Place `facerecognitionsdk.aar` in `libfacesdk/` (see Setup).
2. Open the cloned repo in Android Studio.
3. Run on a **physical** phone (emulator is not recommended for camera / liveness).
4. Wait until the home status bar disappears. Then **Enroll / Identify / Capture / Attribute** unlock.

Keep the demo package name **`com.faceplugin.facerecognitionsdk`** so the sample key works.

| Demo tile     | What it does                                                                        |
| ------------- | ----------------------------------------------------------------------------------- |
| **Enroll**    | Enroll a person from a gallery photo (exactly one face) into the on-device database |
| **Identify**  | Live 1:N camera match (stop on first hit) with 2D liveness                          |
| **Capture**   | Oval coach capture → still with attributes → optional enroll                        |
| **Attribute** | Gallery analysis: landmarks, liveness, pose, quality, age, gender, emotion          |
| **Settings**  | Camera lens, identify / liveness / pose / eye-close thresholds                      |
| **About**     | SDK name and license label                                                          |

### Screenshots

| Home                                                                                                                                                                                                  | Identify                                                                                                                                                                                                   | Capture                                                                                                                                                                                                  |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/android/home.png" alt="Faceplugin Face Recognition Android home" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/android/identify.png" alt="Faceplugin Face Recognition live identify" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/android/capture.png" alt="Faceplugin Face Recognition oval capture" data-size="original"> |

### System requirements

| Item    | Minimum                    | Recommended          |
| ------- | -------------------------- | -------------------- |
| Android | API 24 (7.0)               | API 29 (10) or newer |
| ABI     | `arm64-v8a`, `armeabi-v7a` | `arm64-v8a`          |
| RAM     | 4 GB                       | 6 GB or more         |
| Camera  | Front camera               | 720p or 1080p        |
| Device  | Physical device            | Same                 |

### License

Licenses are **offline**. Keep the demo package name so the sample key works. For your own app, [request a license](../../../../help/request-a-license-and-support.md).

### Try it (after the demo compiles)

```kotlin
Thread {
    FaceRecognitionSDK.getMachineCode(context)
    var code = FaceRecognitionSDK.setActivation(context, DEMO_LICENSE)
    if (code == FaceRecognitionSDK.SDK_SUCCESS) {
        code = FaceRecognitionSDK.init(context)
    }
    val faces = FaceRecognitionSDK.faceDetection(bitmap, FaceDetectionParam())
    if (faces.isNotEmpty()) {
        val t = FaceRecognitionSDK.templateExtraction(bitmap, faces[0])
        val score = FaceRecognitionSDK.similarityCalculation(t, t)
    }
}.start()
```

{% hint style="warning" %}
Call `setActivation`, `init`, and all process methods **off the UI thread**. The engine is **not** concurrent — serialize calls on one thread.
{% endhint %}

### Integrate into your own app

You need `libfacesdk/` (the AAR) and `FaceRecognitionSDK`. You do **not** need the demo Activities.

1. Copy `libfacesdk` into your project root and put `facerecognitionsdk.aar` inside it.
2. Wire Gradle as in Setup (`minSdk 24`, `abiFilters`, `useLegacyPackaging`).
3. Add CAMERA and photo-library permissions.
4. Request license key for your own app.
5. Optional: copy `app/.../kit/` (`FaceRecognitionClient`) so you do not rewrite threading, CameraX, or VideoWorker.

Typical call order: `setActivation` → `init` → `faceDetection` → `templateExtraction` → store templates in **your** database → `similarityCalculation` or VideoWorker for live 1:N.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Which APIs does the Android face recognition SDK provide?**

`setActivation`, `init`, `faceDetection`, `templateExtraction`, `similarityCalculation` and VideoWorker for live 1:N Identify, plus helpers for quality, match and attributes.

**Does the Android face recognition SDK work offline?**

Yes. Once activated with a license key bound to the demo app ID or your own, everything runs on the device.

**What is the default face match threshold?**

The demo's Identify threshold is 0.67. Mobile Identify also includes passive 2D liveness, with a default liveness threshold of 0.5.

**What Android version and devices are supported?**

Android API 24 (7.0) or newer on arm64-v8a or armeabi-v7a, with at least 4 GB RAM. API 29+, arm64-v8a and 6 GB are recommended.

**Can I test on an emulator?**

Use a physical phone. Emulators aren't recommended for camera or liveness.

**Does it detect face attributes?**

Yes. `faceDetection` can return age, gender, emotion, mask, glasses, eye closure, occlusion, pose and quality.

### Related documentation

* [Faceplugin Face Recognition Mobile SDK](./) · [iOS](face-recognition-ios-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Liveness Detection Android SDK](../../liveness-detection-sdk/mobile-sdk/liveness-detection-android-sdk.md)
* [ID Document Recognition Android SDK](../../../id-document-recognition-sdk/recognition-and-liveness/mobile-sdk/id-document-recognition-android-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
