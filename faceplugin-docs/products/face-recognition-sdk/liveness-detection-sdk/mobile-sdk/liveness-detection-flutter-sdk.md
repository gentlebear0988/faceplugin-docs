---
description: >-
  Faceplugin Face Liveness Detection Flutter SDK. On-premise passive and active
  anti-spoofing. face_liveness_sdk plugin for Android and iOS.
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

Fully on-premise **Face Liveness SDK for Flutter**. Package: `face_liveness_sdk`. Passive scoring plus a guided active challenge flow, both running natively on the device.

The camera screens are **native** on each platform — the plugin presents them and hands you back a result. There is nothing to draw in Dart.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceLivenessDetection-Flutter" %}

### How to run

{% stepper %}
{% step %}
#### Install tools

Flutter **3.22+** (Dart SDK 3.3+), JDK **17**, Android Studio and/or Xcode 15+. A physical phone — the engine ships no simulator slice.
{% endstep %}

{% step %}
#### Clone and fetch

```bash
git clone https://github.com/Faceplugin-ltd/FaceLivenessDetection-Flutter.git
cd FaceLivenessDetection-Flutter
flutter pub get
cd example && flutter pub get
```
{% endstep %}

{% step %}
#### Place the native engine

| Platform | File | Path |
| -------- | ---- | ---- |
| Android | `facerecognitionsdk.aar` | `example/android/libfacesdk/` |
| iOS | four framework bundles | `ios/Frameworks/` |

* Android release: [facerecognitionsdk.aar](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Android/releases/tag/latest)
* iOS release: [frameworks](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-iOS/releases/tag/latest) — `facerecognitionsdk`, `FacePluginSDK`, `FacePlugin`, `FaceDetect`

{% hint style="info" %}
On Android the AAR goes in the **`libfacesdk` flat artifact module**, not in `android/libs/`. The Android Gradle Plugin refuses `implementation(files("….aar"))` on a library module, which is why the plugin depends on `:libfacesdk` instead.
{% endhint %}
{% endstep %}

{% step %}
#### Run on a device

```bash
cd example
flutter run
```

Or `python3 run_android.py` / `python3 run_ios.py` from the repository root. Wait for the home status to read **Ready**.
{% endstep %}
{% endstepper %}

Keep the demo ids so the included license works:

| Platform | Id |
| -------- | -- |
| Android package name | `com.faceplugin.faceliveness` |
| iOS bundle identifier | `com.faceplugin.faceliveness.app` |

Minimums: Android **minSdk 24**, iOS **13.0**.

### Start the engine

Activate once at start-up. `FaceLivenessEngine` is a singleton and exposes its state as a `ValueListenable`.

```dart
import 'package:face_liveness_sdk/face_liveness_sdk.dart';

final code = await FaceLivenessEngine.instance.start(licenseKey);
if (code != SdkCode.success) {
  debugPrint(await FaceLivenessEngine.instance.lastLicenseError());
}
```

| Member | What it does |
| ------ | ------------ |
| `start(String license)` | Activates and initialises the engine, returns a status code |
| `state` | `ValueListenable<EngineState>` — `loading`, `ready`, `notActivated`, `licenseInvalid`, `licenseExpired`, `failed` |
| `isReady` | Whether the engine is usable |
| `refreshLicense()` | Re-reads the `LicenseStatus` |
| `machineCode()` | Machine code, to send when requesting a key |
| `lastLicenseError()` / `statusMessage()` | Diagnostics for the UI |

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

### Passive liveness

```dart
await FaceLiveness.startPassiveLiveness(
  useFrontCamera: true,
  livenessThreshold: 0.5,
);
```

Opens the native passive screen with a live score HUD. **0.5 or higher** is treated as Real.

### Active liveness

```dart
final result = await FaceLiveness.startActiveLiveness(
  const ActiveLivenessOptions(count: 3, livenessThreshold: 0.5),
);

if (result == null) {
  // closed before it finished
} else if (result.passed) {
  // live person
} else if (!result.passivePassed) {
  // turned away at the anti-spoofing gate
} else {
  // a challenge was not completed: result.failedCheck
}
```

The attempt runs **align → passive anti-spoofing gate → shuffled challenges**. Below the gate threshold it ends there and no challenge is asked. Losing the face for about a second restarts the whole attempt, gate included.

`ActiveLivenessResult` fields: `passed`, `failure` (`ActiveLivenessFailure.none` / `.spoof` / `.check` / `.timeout` / `.cancelled`), `passiveScore`, `passivePassed`, `challenges`, `outcomes` (`StepState.pending` / `.active` / `.passed` / `.failed`), `durationMs`, plus the computed `failedCheck` and `durationSeconds`.

`ActiveCheck` values: `blink`, `smile`, `turnLeft`, `turnRight`, `turnUp`, `turnDown`, `perspective` (move closer). The default pool is the first six; at most 6 challenges are asked.

### Defaults

`ActiveLivenessOptions` ships the same defaults as every other platform.

| Option | Default |
| ------ | ------- |
| `count` | **3** |
| `randomize` | **true** |
| `livenessThreshold` | **0.5** |
| `passiveSamples` | **3** |
| `framesWait` | **150** |
| `yawThreshold` / `pitchThreshold` | **25°** / **15°** |
| `smileThreshold` / `blinkThreshold` / `blinksNumber` | **0.5** / **0.3** / **2** |
| `perspectiveThreshold` | **0.15** |
| `alignAngle` / `minFaceFraction` / `qualityThreshold` | **12°** / **0.3** / **0.3** |
| `invertTurn` / `invertTilt` | **false** |
| `useFrontCamera` | **true** |

{% hint style="info" %}
**Head-pose direction.** Which way is positive for yaw and pitch is a property of the engine build. If a turn challenge accepts the opposite direction on your device, set `invertTurn` (or `invertTilt`) — no rebuild needed.
{% endhint %}

### Integrate into your own app

There is no pub.dev package. Clone the repository next to your app and depend on the local path:

```yaml
dependencies:
  face_liveness_sdk:
    path: ../FaceLivenessDetection-Flutter
```

Copy the engine into the same paths as above, set **your** application id and bundle identifier, and add the camera permission (`CAMERA` on Android, `NSCameraUsageDescription` on iOS). Then request a license for your own ids.

### Screenshots

| Home | Active liveness | Active result | Passive liveness |
| ---- | --------------- | ------------- | ---------------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/home.gif" alt="Faceplugin Face Liveness Flutter home" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_start.gif" alt="Faceplugin Face Liveness guided active challenge sequence" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_result.png" alt="Faceplugin Face Liveness active result" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/passive.png" alt="Faceplugin Face Liveness passive camera anti-spoofing" data-size="original"> |

### License

Licenses are **offline** and bound to your application id and bundle identifier. The sample key covers the demo ids only. [Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md).

### Frequently Asked Questions

**What is the Flutter face liveness package name?**

`face_liveness_sdk`. It is consumed from a local path, not from pub.dev.

**Can I test it in a simulator or on the web?**

No. The engine is device-only native code with no simulator slice. Run on a physical Android phone or iPhone.

**Does the Flutter SDK support active liveness?**

Yes — `FaceLiveness.startActiveLiveness` runs the anti-spoofing gate and then the challenge sequence, and returns an `ActiveLivenessResult`.

**Do I have to build the camera UI in Dart?**

No. Both liveness screens are native. The plugin presents them and returns the result.

**Does it work offline?**

Yes. Once `start` succeeds with a key for your ids, everything runs on the device.

### Related documentation

* [Faceplugin Face Liveness Detection Mobile SDK](./) · [React Native](liveness-detection-react-native-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition Flutter SDK](../../recognition-and-liveness/mobile-sdk/face-recognition-flutter-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
