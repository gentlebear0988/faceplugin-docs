---
description: >-
  Faceplugin Face Liveness Detection Ionic Cordova SDK. On-premise passive and
  active anti-spoofing. face-liveness-cordova plugin for Cordova projects.
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

# Ionic Cordova

Fully on-premise **Face Liveness SDK for Ionic Cordova**. Plugin id: `face-liveness-cordova`, clobbering `cordova.plugins.FaceLivenessSdk`.

{% hint style="info" %}
For **new** Ionic apps prefer [Ionic Capacitor](liveness-detection-ionic-capacitor-sdk.md). This plugin exists for projects already on Cordova. The TypeScript surface is the same on both.
{% endhint %}

The browser cannot load the engine — run on a **physical** phone. Both camera screens are native.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceLivenessDetection-Ionic-Cordova" %}

### How to run

{% stepper %}
{% step %}
#### Install tools

Node **18+**, JDK **17**, Cordova **12** (`cordova-android` 13, `cordova-ios` 8.1), Android Studio and/or Xcode 15+. A physical phone.
{% endstep %}

{% step %}
#### Clone and install

```bash
git clone https://github.com/Faceplugin-ltd/FaceLivenessDetection-Ionic-Cordova.git
cd FaceLivenessDetection-Ionic-Cordova
npm install
```

The repository root **is** the demo app; the plugin lives in `FacePlugin/`.
{% endstep %}

{% step %}
#### Place the native engine — before adding a platform

| Platform | Files | Path |
| -------- | ----- | ---- |
| Android | `facerecognitionsdk.aar` | `FacePlugin/src/android/` |
| iOS | four **device** `.framework` slices | `FacePlugin/src/ios/Frameworks/` |

The iOS bundles are `facerecognitionsdk.framework`, `FacePluginSDK.framework`, **`FacePPlugin.framework`** and `FaceDetect.framework`.

{% hint style="warning" %}
**`FacePPlugin`, with two Ps.** Cordova embeds the `ios-arm64` slice directly rather than the xcframework, and inside `FacePlugin.xcframework` that slice is named `FacePPlugin.framework`. The plugin's `plugin.xml` and its iOS prepare hook both expect that spelling; using `FacePlugin.framework` fails the build.
{% endhint %}

* Android release: [facerecognitionsdk.aar](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Android/releases/tag/latest)
* iOS release: [frameworks](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-iOS/releases/tag/latest)

Place these **before** adding a platform — the prepare hooks copy and re-sign them, and fail with a named message if any is missing.
{% endstep %}

{% step %}
#### Build and run

```bash
npm run android          # or: npm run ios
```

Wait for the home status to read **Ready**.
{% endstep %}
{% endstepper %}

Keep the demo ids so the included license works:

| Platform | Id |
| -------- | -- |
| Android package name | `com.faceplugin.faceliveness` |
| iOS bundle identifier | `com.faceplugin.faceliveness.app` |

Minimums: Android **minSdk 24**, iOS **14.0**.

### Start the engine

Wait for Cordova's `deviceready` before the first call — the plugin exports a helper for it.

```ts
import { FaceLivenessEngine, SdkCode, deviceReady } from 'face-liveness-cordova';

await deviceReady();
const code = await FaceLivenessEngine.start(licenseKey);
if (code !== SdkCode.success) {
  console.warn(await FaceLivenessEngine.lastLicenseError());
}
```

Calling into the plugin from a desktop browser **rejects** with a clear message rather than failing silently; `isNativeAvailable()` tells you in advance.

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

### Passive liveness

```ts
import { FaceLiveness } from 'face-liveness-cordova';

await FaceLiveness.startPassiveLiveness({
  useFrontCamera: true,
  livenessThreshold: 0.5,
});
```

**0.5 or higher** is treated as Real.

### Active liveness

```ts
import { FaceLiveness, failedCheck } from 'face-liveness-cordova';

const result = await FaceLiveness.startActiveLiveness({ count: 3, livenessThreshold: 0.5 });

if (!result) {
  // closed before it finished
} else if (result.passed) {
  // live person
} else if (!result.passivePassed) {
  // turned away at the anti-spoofing gate
} else {
  console.log(failedCheck(result));
}
```

The attempt runs **align → passive anti-spoofing gate → shuffled challenges**. Below the gate threshold it ends there and no challenge is asked. Losing the face for about a second restarts the whole attempt.

`ActiveLivenessResult`: `passed`, `failure` (`'NONE' | 'SPOOF' | 'CHECK' | 'TIMEOUT' | 'CANCELLED'`), `passiveScore`, `passivePassed`, `challenges`, `outcomes` (`'PENDING' | 'ACTIVE' | 'PASSED' | 'FAILED'`), `durationMs`.

### Plain JavaScript

Without the TypeScript wrapper, the clobbered object exposes the same six calls, each returning a promise:

```js
cordova.plugins.FaceLivenessSdk.start(license)
cordova.plugins.FaceLivenessSdk.licenseStatus()
cordova.plugins.FaceLivenessSdk.lastLicenseError()
cordova.plugins.FaceLivenessSdk.machineCode()
cordova.plugins.FaceLivenessSdk.startActiveLiveness(options)
cordova.plugins.FaceLivenessSdk.startPassiveLiveness(options)
```

### Defaults

Identical to every other platform: `count` **3**, `randomize` **true**, `livenessThreshold` **0.5**, `passiveSamples` **3**, `framesWait` **150**, `yawThreshold` **25**, `pitchThreshold` **15**, `smileThreshold` **0.5**, `blinkThreshold` **0.3**, `blinksNumber` **2**, `perspectiveThreshold` **0.15**, `alignAngle` **12**, `minFaceFraction` **0.3**, `qualityThreshold` **0.3**, `invertTurn` / `invertTilt` **false**, `useFrontCamera` **true**.

### Integrate into your own app

Add the plugin from a local path, with the engine already in place:

```bash
cordova plugin add ../FaceLivenessDetection-Ionic-Cordova/FacePlugin
```

Set **your** `id` in `config.xml`, keep `android-minSdkVersion` at 24 and the iOS deployment target at 14.0, and request a license for your own ids.

### Screenshots

| Home | Active liveness | Active result | Passive liveness |
| ---- | --------------- | ------------- | ---------------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/home.gif" alt="Faceplugin Face Liveness Cordova home" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_start.gif" alt="Faceplugin Face Liveness guided active challenge sequence" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_result.png" alt="Faceplugin Face Liveness active result" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/passive.png" alt="Faceplugin Face Liveness passive camera anti-spoofing" data-size="original"> |

### License

Licenses are **offline** and bound to your application id and bundle identifier. The sample key covers the demo ids only. [Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md).

### Frequently Asked Questions

**What is the Cordova face liveness plugin id?**

`face-liveness-cordova`. It clobbers `cordova.plugins.FaceLivenessSdk`.

**Should I use Cordova or Capacitor?**

Capacitor for new Ionic apps. Use this plugin only for projects already on Cordova.

**Why does the plugin reject in the browser?**

The engine is native code. The plugin rejects with a clear message off-device rather than failing silently; check `isNativeAvailable()` first.

**Does the Cordova SDK support active liveness?**

Yes — `FaceLiveness.startActiveLiveness` runs the anti-spoofing gate and then the challenge sequence.

**Does it work offline?**

Yes. Once `start` succeeds with a key for your ids, everything runs on the device.

### Related documentation

* [Faceplugin Face Liveness Detection Mobile SDK](./) · [Ionic Capacitor](liveness-detection-ionic-capacitor-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition Ionic Cordova SDK](../../recognition-and-liveness/mobile-sdk/face-recognition-ionic-cordova-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
