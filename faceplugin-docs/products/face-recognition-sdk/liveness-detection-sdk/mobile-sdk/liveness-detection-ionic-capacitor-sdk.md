---
description: >-
  Faceplugin Face Liveness Detection Ionic Capacitor SDK. On-premise passive and
  active anti-spoofing. face-liveness-capacitor plugin for new Ionic apps.
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

# Ionic Capacitor

Fully on-premise **Face Liveness SDK for Ionic Capacitor**. Package: `face-liveness-capacitor`, plugin id `FaceLivenessSdk`. Use this for **new** Ionic apps — Cordova is a [different plugin](liveness-detection-ionic-cordova-sdk.md).

{% hint style="warning" %}
`ionic serve` and the browser **cannot** load the engine. Open Android Studio or Xcode and run on a **physical** phone.
{% endhint %}

Both camera screens are **native**. The plugin presents them and hands you back a result.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceLivenessDetection-Ionic-Capacitor" %}

### How to run

{% stepper %}
{% step %}
#### Install tools

Node **18+**, JDK **17**, Android Studio and/or Xcode 15+. Capacitor **6**. A physical phone with USB debugging.
{% endstep %}

{% step %}
#### Clone

```bash
git clone https://github.com/Faceplugin-ltd/FaceLivenessDetection-Ionic-Capacitor.git
cd FaceLivenessDetection-Ionic-Capacitor
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
On Android the AAR goes in the **`libfacesdk` flat artifact module**, not in `android/libs/` — the Android Gradle Plugin refuses `implementation(files("….aar"))` on a library module. On iOS, the `ios-arm64` slice of `FacePlugin.xcframework` is named **`FacePPlugin.framework`**, with two Ps; the podspec links that name.
{% endhint %}
{% endstep %}

{% step %}
#### Run on a device

```bash
python run_android.py        # or: python run_ios.py on macOS
```

That is the whole setup. The script runs `npm install`, builds the plugin and the example, runs `npx cap sync`, and installs onto the plugged-in phone. Wait for the home status to read **Ready**.
{% endstep %}
{% endstepper %}

**Running it by hand.** The script is a convenience, not a requirement — the same sequence, with the runtime already in place:

```bash
npm install && npm run build
cd example && npm install && npm run build
npx cap sync android && npx cap run android
```

Keep the demo ids so the included license works:

| Platform | Id |
| -------- | -- |
| Android package name | `com.faceplugin.faceliveness` |
| iOS bundle identifier | `com.faceplugin.faceliveness.app` |

Minimums: Android **minSdk 24**, iOS **14.0**.

### Start the engine

```ts
import { FaceLivenessEngine, SdkCode } from 'face-liveness-capacitor';

const code = await FaceLivenessEngine.start(licenseKey);
if (code !== SdkCode.success) {
  console.warn(await FaceLivenessEngine.lastLicenseError());
}
```

| Member | What it does |
| ------ | ------------ |
| `start(license)` | Activates and initialises the engine, resolves to a status code |
| `state` / `isReady` / `lastCode` | Current engine state |
| `addListener(fn)` | Subscribe to state changes |
| `refreshLicense()` | Re-reads the `LicenseStatus` |
| `machineCode()` | Machine code, to send when requesting a key |
| `lastLicenseError()` / `statusMessage()` | Diagnostics for the UI |

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

### Passive liveness

```ts
import { FaceLiveness } from 'face-liveness-capacitor';

await FaceLiveness.startPassiveLiveness({
  useFrontCamera: true,
  livenessThreshold: 0.5,
});
```

**0.5 or higher** is treated as Real.

### Active liveness

```ts
import { FaceLiveness, failedCheck } from 'face-liveness-capacitor';

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

`ActiveCheckKey`: `'blink'`, `'smile'`, `'turn_left'`, `'turn_right'`, `'turn_up'`, `'turn_down'`, `'perspective'`. `DEFAULT_POOL` is the first six.

### Raw bridge

`FaceLivenessSdk` is the unwrapped Capacitor plugin if you prefer to drive it yourself:

```ts
start({ license }) → { code }
licenseStatus() → LicenseStatus
lastLicenseError() → { message }
machineCode() → { code }
startActiveLiveness(options) → { result? }
startPassiveLiveness({ useFrontCamera?, livenessThreshold? })
```

### Defaults

`DEFAULT_OPTIONS` matches every other platform: `count` **3**, `randomize` **true**, `livenessThreshold` **0.5**, `passiveSamples` **3**, `framesWait` **150**, `yawThreshold` **25**, `pitchThreshold` **15**, `smileThreshold` **0.5**, `blinkThreshold` **0.3**, `blinksNumber` **2**, `perspectiveThreshold` **0.15**, `alignAngle` **12**, `minFaceFraction` **0.3**, `qualityThreshold` **0.3**, `invertTurn` / `invertTilt` **false**, `useFrontCamera` **true**.

### Integrate into your own app

There is no npm registry package. Clone the repository next to your app, build the plugin once, and depend on that local copy:

```json
{
  "dependencies": {
    "face-liveness-capacitor": "file:../FaceLivenessDetection-Ionic-Capacitor"
  }
}
```

```bash
npm install
npx cap sync
```

Copy the engine into the plugin folder as in the table above, run `npx cap sync` again, set **your** `appId` in `capacitor.config.ts`, and add the camera permission. Then request a license for your own ids.

### Screenshots

| Home | Active liveness | Active result | Passive liveness |
| ---- | --------------- | ------------- | ---------------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/home.gif" alt="Faceplugin Face Liveness Ionic home" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_start.gif" alt="Faceplugin Face Liveness guided active challenge sequence" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_result.png" alt="Faceplugin Face Liveness active result" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/passive.png" alt="Faceplugin Face Liveness passive camera anti-spoofing" data-size="original"> |

### License

Licenses are **offline** and bound to your application id and bundle identifier. The sample key covers the demo ids only. [Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md).

### Frequently Asked Questions

**What is the Ionic Capacitor face liveness package name?**

`face-liveness-capacitor`. Capacitor is preferred over Cordova for new Ionic apps.

**Can I test it with `ionic serve` or in a browser?**

No. The engine only loads on a real device. Run the app on a physical phone from Android Studio or Xcode.

**Should I use Capacitor or Cordova?**

Use Capacitor for new Ionic apps. The Cordova version is a separate plugin for existing Cordova projects.

**Does the Ionic SDK support active liveness?**

Yes — `FaceLiveness.startActiveLiveness` runs the anti-spoofing gate and then the challenge sequence.

**Does it work offline?**

Yes. Once `start` succeeds with a key for your ids, everything runs on the device.

### Related documentation

* [Faceplugin Face Liveness Detection Mobile SDK](./) · [Ionic Cordova](liveness-detection-ionic-cordova-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition Ionic Capacitor SDK](../../recognition-and-liveness/mobile-sdk/face-recognition-ionic-capacitor-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
