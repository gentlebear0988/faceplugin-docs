---
description: >-
  Faceplugin Face Liveness Detection React Native SDK. On-premise passive and
  active anti-spoofing. face-liveness-sdk plugin for Android and iOS.
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

# React Native

Fully on-premise **Face Liveness SDK for React Native**. Package: `face-liveness-sdk`, native module `FaceLivenessSdk`. Passive scoring plus a guided active challenge flow, both running natively.

{% hint style="warning" %}
**Not Expo Go.** The SDK is native code with no simulator slice. Use a bare React Native project or an Expo development build, and run on a **physical** phone.
{% endhint %}

The camera screens are **native** on each platform. The plugin presents them and hands you back a result — there is nothing to draw in JavaScript.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceLivenessDetection-ReactNative" %}

### How to run

{% stepper %}
{% step %}
#### Install tools

Node **18+**, JDK **17**, Android Studio and/or Xcode 15+. A physical phone with USB debugging. The example app is built on React Native **0.74.5**.
{% endstep %}

{% step %}
#### Clone

```bash
git clone https://github.com/Faceplugin-ltd/FaceLivenessDetection-ReactNative.git
cd FaceLivenessDetection-ReactNative
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

The podspec fails the build with the missing list if any bundle is absent.
{% endstep %}

{% step %}
#### Run on a device

```bash
python run_android.py        # or: python run_ios.py on macOS
```

That is the whole setup. The script runs the yarn workspace install, generates the JS bundle, runs `pod install` on macOS, builds, and installs onto the plugged-in phone. Wait for the home status to read **Ready**.
{% endstep %}
{% endstepper %}

**Running it by hand.** The script is a convenience, not a requirement — the same sequence, with the runtime already in place:

```bash
yarn install
cd example && yarn android      # or: yarn ios
```

Keep the demo ids so the included license works:

| Platform | Id |
| -------- | -- |
| Android package name | `com.faceplugin.faceliveness` |
| iOS bundle identifier | `com.faceplugin.faceliveness.app` |

Android **minSdk 24**, `targetSdk 34`.

### Start the engine

```ts
import { FaceLivenessEngine, SdkCode } from 'face-liveness-sdk';

const code = await FaceLivenessEngine.start(licenseKey);
if (code !== SdkCode.success) {
  console.warn(await FaceLivenessEngine.lastLicenseError());
}

const unsubscribe = FaceLivenessEngine.addListener((state) => {
  // 'loading' | 'ready' | 'notActivated' | 'licenseInvalid' | 'licenseExpired' | 'failed'
});
```

| Member | What it does |
| ------ | ------------ |
| `start(license)` | Activates and initialises the engine, resolves to a status code |
| `state` / `isReady` / `lastCode` | Current engine state |
| `addListener(fn)` | Subscribe to state changes; returns an unsubscribe function |
| `refreshLicense()` | Re-reads the `LicenseStatus` |
| `machineCode()` | Machine code, to send when requesting a key |
| `lastLicenseError()` / `statusMessage()` | Diagnostics for the UI |

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

### Passive liveness

```ts
import { FaceLiveness } from 'face-liveness-sdk';

await FaceLiveness.startPassiveLiveness({
  useFrontCamera: true,
  livenessThreshold: 0.5,
});
```

Opens the native passive screen with a live score HUD. **0.5 or higher** is treated as Real.

### Active liveness

```ts
import { FaceLiveness, failedCheck } from 'face-liveness-sdk';

const result = await FaceLiveness.startActiveLiveness({ count: 3, livenessThreshold: 0.5 });

if (!result) {
  // closed before it finished
} else if (result.passed) {
  // live person
} else if (!result.passivePassed) {
  // turned away at the anti-spoofing gate
} else {
  // a challenge was not completed
  console.log(failedCheck(result));
}
```

The attempt runs **align → passive anti-spoofing gate → shuffled challenges**. Below the gate threshold it ends there and no challenge is asked. Losing the face for about a second restarts the whole attempt, gate included.

`ActiveLivenessResult`: `passed`, `failure` (`'NONE' | 'SPOOF' | 'CHECK' | 'TIMEOUT' | 'CANCELLED'`), `passiveScore`, `passivePassed`, `challenges`, `outcomes` (`'PENDING' | 'ACTIVE' | 'PASSED' | 'FAILED'`), `durationMs`.

`ActiveCheckKey`: `'blink'`, `'smile'`, `'turn_left'`, `'turn_right'`, `'turn_up'`, `'turn_down'`, `'perspective'` (move closer). `DEFAULT_POOL` is the first six; `checkInfo(key)` gives the title and hint for your own UI.

### Defaults

`DEFAULT_OPTIONS` matches every other platform.

| Option | Default |
| ------ | ------- |
| `count` / `randomize` | **3** / **true** |
| `livenessThreshold` | **0.5** |
| `passiveSamples` / `framesWait` | **3** / **150** |
| `yawThreshold` / `pitchThreshold` | **25** / **15** |
| `smileThreshold` / `blinkThreshold` / `blinksNumber` | **0.5** / **0.3** / **2** |
| `perspectiveThreshold` | **0.15** |
| `alignAngle` / `minFaceFraction` / `qualityThreshold` | **12** / **0.3** / **0.3** |
| `invertTurn` / `invertTilt` | **false** / **false** |
| `useFrontCamera` | **true** |

{% hint style="info" %}
**Head-pose direction.** Which way is positive for yaw and pitch is a property of the engine build. If a turn challenge accepts the opposite direction on your device, set `invertTurn` (or `invertTilt`).
{% endhint %}

### Integrate into your own app

There is no npm registry package. Clone the repository next to your app and depend on the local copy:

```json
{
  "dependencies": {
    "face-liveness-sdk": "file:../FaceLivenessDetection-ReactNative"
  }
}
```

Then `yarn install` and, on iOS, `cd ios && pod install`. Copy the engine into the paths above, set **your** application id and bundle identifier, add the camera permission (`CAMERA` on Android, `NSCameraUsageDescription` on iOS), and request a license for your own ids.

### Screenshots

| Home | Active liveness | Active result | Passive liveness |
| ---- | --------------- | ------------- | ---------------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/home.gif" alt="Faceplugin Face Liveness React Native home" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_start.gif" alt="Faceplugin Face Liveness guided active challenge sequence" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_result.png" alt="Faceplugin Face Liveness active result" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/passive.png" alt="Faceplugin Face Liveness passive camera anti-spoofing" data-size="original"> |

### License

Licenses are **offline** and bound to your application id and bundle identifier. The sample key covers the demo ids only. [Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md).

### Frequently Asked Questions

**What is the React Native face liveness package name?**

`face-liveness-sdk`, exposing the native module `FaceLivenessSdk`. It is consumed from a local path, not from npm.

**Does it work with Expo Go?**

No. Expo Go cannot load custom native code. Use a bare project or an Expo development build on a physical device.

**Does the React Native SDK support active liveness?**

Yes — `FaceLiveness.startActiveLiveness` runs the anti-spoofing gate and then the challenge sequence, and resolves to an `ActiveLivenessResult`.

**Do I have to build the camera UI in JavaScript?**

No. Both liveness screens are native. The plugin presents them and returns the result.

**Does it work offline?**

Yes. Once `start` succeeds with a key for your ids, everything runs on the device.

### Related documentation

* [Faceplugin Face Liveness Detection Mobile SDK](./) · [Flutter](liveness-detection-flutter-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition React Native SDK](../../recognition-and-liveness/mobile-sdk/face-recognition-react-native-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
