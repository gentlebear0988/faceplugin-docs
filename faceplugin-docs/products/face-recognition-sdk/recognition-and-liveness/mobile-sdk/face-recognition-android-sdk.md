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

You integrate through **`face-recognition-kit`**: `FaceEngine` for the lifecycle, `FaceGallery` for enrol and 1:N identify, `FaceDetector` for a single image, and `FaceLimits` for the thresholds. `face-recognition-capture` adds the guided capture screen. The demo app has five tabs — Identify, People, Attribute, Liveness and Settings — and is built on exactly these libraries.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Android" %}

### Setup <a href="#setup" id="setup"></a>

You integrate from the repository itself. There is no Maven coordinate and no package-server dependency.

1. Clone the repository, and download `facerecognitionsdk.aar` from the [GitHub release](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Android/releases/tag/latest) into `libs/`:

```
FaceRecognition-LivenessDetection-Android/
└── libs/
    └── facerecognitionsdk.aar
```

2. Copy the two library modules into your project, and add them in `settings.gradle`

```
rootProject.name = "YourProjectName"
include ':app'
include ':face-recognition-kit'
include ':face-recognition-capture'   // optional capture coach
```

3. Add the dependencies to your `app/build.gradle`. The app supplies the engine, because an Android library cannot package a local `.aar`.

```
android {
    defaultConfig {
        minSdk 24
        ndk { abiFilters 'arm64-v8a', 'armeabi-v7a' }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_17
        targetCompatibility JavaVersion.VERSION_17
    }
    packaging {
        jniLibs { useLegacyPackaging = true }
    }
}
dependencies {
    implementation project(':face-recognition-kit')
    implementation project(':face-recognition-capture')
    implementation files(rootProject.file('libs/facerecognitionsdk.aar'))
}
```

4. Add camera / gallery permissions in `AndroidManifest.xml`. Start the engine once per process on a **background** thread. Keep the demo `applicationId` **`com.faceplugin.facerecognitionsdk`** while you use the bundled key, and request a new license key for your own app.

The engine is about 80 MB, so use ABI splits or an app bundle for release builds.

{% hint style="info" %}
Serialize native calls on one thread. The engine is not concurrent. First `init` unpacks on-device models (a few seconds).
{% endhint %}

Status codes: **0** Success (`SDK_SUCCESS`), **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

### Quick start

Everything below is the **kit** API — `face-recognition-kit`, the library the demo app itself is built on. It owns the engine lifecycle, serializes every native call, and keeps the enrolled people in SQLite for you. The raw engine is underneath it, documented in [Engine API reference](#engine-api-reference), but most apps never touch it.

#### 1. Start the engine once per process

```kotlin
class App : Application() {
    override fun onCreate() {
        super.onCreate()
        FaceEngine.start(this, BuildConfig.FACEPLUGIN_LICENSE) { code ->
            // code == 0 when the engine is ready. Activation and model loading
            // already happened on the engine's own thread.
        }
    }
}
```

Watch the state from any screen, and keep the camera disabled until it is ready:

```kotlin
FaceEngine.addListener { state ->
    when (state) {
        FaceEngine.State.READY -> enableCamera()
        FaceEngine.State.LOADING -> showSpinner()
        FaceEngine.State.NOT_ACTIVATED,
        FaceEngine.State.LICENSE_INVALID,
        FaceEngine.State.LICENSE_EXPIRED -> showLicenseError(FaceEngine.stateMessage(state))
        else -> showLicenseError(FaceEngine.stateMessage(state))
    }
}

FaceEngine.isReady                 // poll instead, if you prefer
FaceEngine.licenseStatus()         // what the key unlocks
FaceEngine.machineCode(context)    // send this when you request a key
```

#### 2. Set your thresholds once

`FaceLimits` carries every threshold the kit uses, so the library never reads your settings store:

```kotlin
val limits = FaceLimits(
    match = 0.80f,      // lowest similarity that counts as the same person
    liveness = 0.50f,   // lowest liveness score that counts as a live face
    yaw = 20f, roll = 20f, pitch = 20f,
)

limits.isLive(face.liveness)                       // one rule, used everywhere
limits.poseOk(face.yaw, face.roll, face.pitch)
limits.matches(similarity)
```

`FaceLimits.DEFAULT` is the engine's operating point. Pass the same instance to enroll, identify and the capture screen so they cannot disagree.

#### 3. Enrol a person

```kotlin
// Off the main thread — FaceEngine.worker is one.
when (val outcome = FaceGallery.get(context).enroll("Mina Ortiz", "Front desk", photo, limits)) {
    is EnrollOutcome.Enrolled -> {
        val person = outcome.record          // id, name, note, photo path
        outcome.duplicates.forEach { dup ->  // everyone already enrolled who matches
            Log.w("enroll", "also matches ${dup.name} at ${dup.similarity}")
        }
        outcome.report                       // every engine call it made, and how long each took
    }
    is EnrollOutcome.Rejected -> toast(outcome.reason)   // no face, bad pose, no template…
}
```

`duplicates` is the **full** list at or above the match threshold, highest first — one new face can match several existing people, and showing only the closest makes a gallery look cleaner than it is.

#### 4. Identify against everyone enrolled (1:N)

```kotlin
when (val outcome = FaceGallery.get(context).identify(probe, limits, livenessOn = true)) {
    is IdentifyOutcome.Done -> {
        val result = outcome.result
        for (face in result.faces) {                 // every face found in the probe
            val best = face.best                     // Candidate: id, name, note, similarity
            if (best != null && face.matched(result.threshold)) {
                show("${best.name} at ${"%.2f".format(best.similarity)}")
            } else {
                show("No match")
            }
            face.matches(result.threshold)           // everyone at or above the threshold
        }
        result.extractMs; result.searchMs; result.gallerySize
    }
    is IdentifyOutcome.Rejected -> toast(outcome.reason)  // no face, liveness failed, no template
}
```

With `livenessOn = true` a spoofed face is rejected **before** the gallery is searched, so a printout never reaches the matcher.

#### 5. Read a single face, without the gallery

```kotlin
val faces = FaceDetector.detect(bitmap, FaceDetectionParam.allAttributes())
val face = faces.firstOrNull() ?: return

face.liveness; face.face_quality
face.yaw; face.roll; face.pitch
face.age; face.genderLabel; face.emotionLabel; face.maskLabel
face.landmarkCount; face.landmarks_68

val template = FaceDetector.template(bitmap, face)          // ByteArray, store it yourself
val score = FaceDetector.similarity(templateA, templateB)   // 1:1
```

Use this path when you keep templates in **your** database instead of the kit's gallery.

#### 6. Browse and maintain the gallery

```kotlin
val gallery = FaceGallery.get(context)
gallery.count(query = "")                       // how many people match a search
gallery.page(query = "mina", limit = 20, offset = 0)
gallery.get(id)
gallery.update(id, name = "Mina Ortiz", note = "Reception")
gallery.delete(id)
gallery.addListener { refreshList() }           // fires whenever the gallery changes
```

Enrolled people live in SQLite with their photos in app-private storage, and ids are never reused.

#### 7. The capture coach (optional)

`face-recognition-capture` gives you the oval guide, live coaching and a hold-to-capture that only fires on a good frame:

```kotlin
class AddPersonActivity : FaceCaptureActivity() {

    override fun resultPanelLayout() = R.layout.view_enroll_panel

    override fun limits() = myLimits

    override fun onCaptureAnalyzed(face: FaceBox?, faceCount: Int) {
        val photo = capturedPhoto() ?: return
        // resultPanel() is your inflated layout; retakeCapture() returns to the camera
    }
}
```

#### 8. Show what actually happened

`enroll`, `identify` and `attributes` all return a `CallReport` — useful in support tickets, and in your own logs:

```kotlin
report.success
report.totalMs
report.steps.forEach { step ->
    Log.d("faceplugin", "${step.name} ${step.ms} ms ok=${step.ok} ${step.detail}")
}
```

{% hint style="info" %}
Call the kit from any background thread. Every engine call is serialized on one lock inside `FaceEngine`, so you do not have to coordinate threads yourself — but do not call it from the main thread, because the first `init` unpacks the on-device models.
{% endhint %}

### Engine API reference

Below is the **raw engine** class the kit wraps, `com.faceplugin.facerecognitionsdk.FaceRecognitionSDK`. Use it only when you are replacing the kit — for example when you already have your own camera pipeline and person database.


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
4. Wait until the home status bar disappears. Then the Identify, People, Attribute and Liveness tabs unlock.

Keep the demo package name **`com.faceplugin.facerecognitionsdk`** so the sample key works.

| Tab | What it does |
| --- | ------------ |
| **Identify** | Live 1:N against everyone enrolled, with passive 2D liveness, then a ranked result screen |
| **People** | Enroll one person with the capture coach or many from photos; search, edit, delete |
| **Attribute** | Every face in a photo: liveness, quality, detection score, pose, expression, age, gender, landmarks |
| **Liveness** | Passive anti-spoofing check on the live camera feed |
| **Settings** | Match, liveness and pose thresholds, and the license status |

### Screenshots

The demo is the same five-tab app on every platform.

| Identify | People | Attribute |
| -------- | ------ | --------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/identify.gif" alt="Faceplugin Face Recognition live 1:N identify with 2D liveness" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/people.gif" alt="Faceplugin Face Recognition enrolling people with the capture coach" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/attribute.gif" alt="Faceplugin Face Recognition face attributes, quality and landmarks" data-size="original"> |

| Liveness | Settings |
| -------- | -------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/liveness.gif" alt="Faceplugin passive 2D liveness check on the camera feed" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/settings.png" alt="Faceplugin Face Recognition match, liveness and pose thresholds" data-size="original"> |
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
