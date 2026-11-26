---
description: >-
  Faceplugin Face Recognition Flutter SDK. On-premise 1:1 and 1:N matching with
  2D liveness. face_recognition_sdk plugin for Android and iOS.
icon: mobile
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

# Flutter

Fully on-premise **Face Recognition SDK for Flutter**. Package: `face_recognition_sdk`. Same native engines as the Android and iOS Face Recognition SDKs.

All processing stays on the device. **No** biometric data is sent to Faceplugin cloud.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Flutter" %}

### How to Run

{% stepper %}
{% step %}
#### Install tools

Flutter **3.44+** and Dart **3.3+** (from the package `pubspec.yaml`), JDK **17+** for Android. Run `flutter doctor` and fix reported issues. Accept Android licenses: `flutter doctor --android-licenses`. `JAVA_HOME` must point to the JDK **home** (the folder that contains `bin`), not to `bin` itself.
{% endstep %}

{% step %}
#### Clone

```bash
git clone https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Flutter.git
cd FaceRecognition-LivenessDetection-Flutter
```
{% endstep %}

{% step %}
#### Place native runtimes

| Platform | File                     | Example app path              | Your own app path                                                                                       |
| -------- | ------------------------ | ----------------------------- | ------------------------------------------------------------------------------------------------------- |
| Android  | `facerecognitionsdk.aar` | `example/android/libfacesdk/` | Copy `example/android/libfacesdk/` into **your app** `android/libfacesdk/` and `include(":libfacesdk")` |
| iOS      | three frameworks         | `ios/Frameworks/`             | Plugin checkout `ios/Frameworks/`                                                                       |

* Android GitHub release: [facerecognitionsdk.aar](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Android/releases/tag/latest)
* iOS GitHub release: [frameworks](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-iOS/releases/tag/latest) (`facerecognitionsdk`, `FaceRecognitionEngine`, `onnxruntime`)

{% hint style="warning" %}
Do **not** `implementation(files(…aar))` inside the plugin — AGP will fail `bundleDebugAar`. Copy the `libfacesdk` **module** from the example into your app.
{% endhint %}

On macOS, `run_ios.py` extracts the `ios-arm64` slice from each xcframework and runs `pod install` for you. Doing it by hand instead, plain `.framework` alone often leaves undefined `FaceRecognitionSDK` symbols under `use_frameworks!`.
{% endstep %}

{% step %}
#### Run the example

```bash
python run_android.py        # or: python run_ios.py on macOS
```

That is the whole setup. The script runs `flutter pub get` in the plugin and the example, `pod install` on macOS, builds, and installs onto the plugged-in phone. Wait for the home status bar → **Ready**.
{% endstep %}
{% endstepper %}

**Running it by hand.** The script is a convenience, not a requirement — the same sequence, with the runtime already in place:

```bash
flutter pub get
cd example && flutter pub get && flutter run
```

Keep demo ids so the included license works:

| Platform              | Id                                   |
| --------------------- | ------------------------------------ |
| Android package name  | `com.faceplugin.facerecognitionsdk`  |
| iOS bundle identifier | `com.faceplugin.facerecognition.app` |

| Requirement | Value                                  |
| ----------- | -------------------------------------- |
| Flutter     | 3.44+ (see `pubspec.yaml`)             |
| Android     | minSdk 24, physical device recommended |
| iOS         | iOS 13+, physical device recommended   |

The demo app has five tabs: Identify, People, Attribute, Liveness and Settings.

### Screenshots

The demo is the same five-tab app on every platform.

| Identify | People | Attribute |
| -------- | ------ | --------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/identify.gif" alt="Faceplugin Face Recognition live 1:N identify with 2D liveness" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/people.gif" alt="Faceplugin Face Recognition enrolling people with the capture coach" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/attribute.gif" alt="Faceplugin Face Recognition face attributes, quality and landmarks" data-size="original"> |

| Liveness | Settings |
| -------- | -------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/liveness.gif" alt="Faceplugin passive 2D liveness check on the camera feed" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/settings.png" alt="Faceplugin Face Recognition match, liveness and pose thresholds" data-size="original"> |
### License

Licenses are **offline**. Demo keys live in `example/lib/core/constants/license.dart`. Request a new license key for your own app.

Default thresholds in the example Settings: identify **0.67**, liveness **0.5**, pose **40°**, eye-close **0.5**. You pass these as Dart parameters — change them for your product.

[Request a License & Support](../../../../help/request-a-license-and-support.md).

### Integrate into your own app

You need the Flutter plugin + native runtimes. You do **not** need the example UI or person database.

Clone the repository next to your app and depend on that local copy. There is no pub.dev package.

```yaml
dependencies:
  face_recognition_sdk:
    path: ../FaceRecognition-LivenessDetection-Flutter
```

Then copy runtimes (table above), set your Android package name / iOS bundle identifier, add camera + photo-library permissions, and request a license for your own app.

```dart
import 'package:face_recognition_sdk/face_recognition_sdk.dart';

Future<void> activate() async {
  final code = await getMachineCode(); // machine code — send when requesting a key
  final act = await setActivation('license key');
  if (act != sdkSuccess) {
    throw StateError(await lastLicenseError());
  }
  final initCode = await init();
  if (initCode != sdkSuccess) {
    throw StateError('init failed: $initCode');
  }
}

Future<void> enrollStill(String imagePath) async {
  final faces = await faceDetection(imagePath);
  if (faces.length != 1) return;
  final template = await templateExtraction(imagePath, faces.first);
  final cropB64 = await cropFace(imagePath, faces.first);
  // Store template + crop in YOUR database
}
```

Optional oval UI: `FaceCapture` from `package:face_recognition_sdk/capture`.

### Worked example

A complete enrol-then-identify flow. Store the templates wherever your app already keeps user data — the plugin does not own a gallery.

```dart
import 'package:face_recognition_sdk/face_recognition_sdk.dart';

// 1. Once at start-up.
Future<String> boot(String license) async {
  final machine = await getMachineCode();        // send this when you request a key
  final act = await setActivation(license);
  if (act != sdkSuccess) throw StateError(await lastLicenseError());
  final code = await init();
  if (code != sdkSuccess) throw StateError('init failed: $code');
  return getLicenseStatus();                     // what the key unlocks
}

// 2. Enrol one person from a gallery photo.
Future<void> enroll(String imagePath, String name) async {
  final faces = await faceDetection(imagePath);
  if (faces.length != 1) throw StateError('use a photo with exactly one face');

  final face = faces.first;
  if (face.liveness < 0.5) throw StateError('that photo looks like a spoof');

  final template = await templateExtraction(imagePath, face);
  final avatar = await cropFace(imagePath, face);
  await db.savePerson(name: name, template: template, avatar: avatar);  // your storage
}

// 3. Identify on the live camera, 1:N against everyone you stored.
Future<void> startIdentify(void Function(String, double) onMatch) async {
  final people = await db.allPeople();
  await startVideoWorker(const VideoWorkerConfig(matchThreshold: 0.67));
  await syncVideoWorkerDatabase([for (final p in people) p.template], 0.67);

  // feed camera frames:
  // final result = await applyLiveFrame(bytes, rotateDegrees: 90, maxEdge: 640, feedWorker: true);
}

// 4. Or compare two templates yourself, 1:1.
final score = await similarity(templateA, templateB);
final samePerson = score >= 0.67;

// 5. Always release the engine when the screen goes away.
await stopVideoWorker();
```

Thresholds are arguments, not settings the plugin reads: identify **0.67**, liveness **0.5**, pose **40°**, eye-close **0.5** are the example's defaults.

### APIs

```dart
import 'package:face_recognition_sdk/face_recognition_sdk.dart';
```

Call order: `getMachineCode` → `setActivation` → `init` → detect / VideoWorker → `deinit`.

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

#### <mark style="color:orange;">setActivation:</mark> This API is used to activate the SDK

```dart
Future<int> setActivation(String license)
```

#### <mark style="color:orange;">init:</mark> This API is used to initialize the SDK

```dart
Future<int> init()
```

#### <mark style="color:orange;">getMachineCode:</mark> This API is used to retrieve the machine code

```dart
Future<String> getMachineCode()
```

#### <mark style="color:orange;">faceDetection:</mark> This API is used to detect faces

```dart
Future<List<FaceBox>> faceDetection(String imageUri, [FaceDetectionParam param])
```

Boxes include geometry (`x1,y1,x2,y2`), pose, liveness / quality / eyes / occlusion, age / gender / emotion / mask / glasses, optional `attributes`, and `landmarks` + `landmarkCount`.

#### <mark style="color:orange;">templateExtraction:</mark> This API is used to extract face template

```dart
Future<String> templateExtraction(String imageUri, FaceBox faceBox)
```

#### <mark style="color:orange;">similarity:</mark> This API is used to calculate similarity between two templates

```dart
Future<double> similarity(String feature1B64, String feature2B64)
```

#### <mark style="color:orange;">detect:</mark> This API is used to detect faces and return engine JSON

```dart
Future<String> detect(ImageInput image, {bool crop = false, int flags = DETECT_ALL})
```

Also: `extractFeature`, `quality`, `cropFace`, `setLandmarkMode` / `getLandmarkMode`, `getLicenseStatus`, `lastLicenseError`, `deinit`.

#### <mark style="color:orange;">VideoWorker:</mark> This API is used for live 1:N identify

```dart
Future<int> startVideoWorker(VideoWorkerConfig config)
Future<int> syncVideoWorkerDatabase(List<String> features, {double matchThreshold = 0.67})
Future<LiveFrameResult> ingestLiveCameraFrame(LiveCameraPhoto photo, LiveFrameOptions options)
Stream<String> get videoWorkerEvents
Future<void> stopVideoWorker()
```

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**What is the Flutter face recognition package name?**

`face_recognition_sdk`, added as a git dependency from the FaceRecognition-LivenessDetection-Flutter repository.

**Does the Flutter face recognition SDK work offline?**

Yes. Detection, matching and liveness all run on the device, with no cloud.

**Which Flutter version do I need?**

Flutter 3.44+ and Dart 3.3+, with JDK 17+ for Android builds.

**Is there a Face Liveness SDK for Flutter?**

There is no public standalone one. Use the 2D liveness built into Identify, or the native Android and iOS Liveness SDKs.

**What is the default match threshold?**

0.67 for Identify and 0.5 for liveness. Both are Dart parameters you can change.

**Are the screenshots Flutter-specific?**

No. They show the Android sample UI; the plugin wraps the native Android and iOS engines.

### Related documentation

* [Faceplugin Face Recognition Mobile SDK](./) · [React Native](face-recognition-react-native-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [ID Document Recognition Flutter SDK](../../../id-document-recognition-sdk/recognition-and-liveness/mobile-sdk/id-document-recognition-flutter-sdk.md)
* There is no public Face Liveness Flutter SDK — use Identify 2D liveness or native Android/iOS Liveness.
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
