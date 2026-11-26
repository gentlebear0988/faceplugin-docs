---
description: >-
  Faceplugin Face Liveness Detection iOS SDK. On-premise passive and active PAD
  against photos, screens, masks, and deepfakes.
icon: apple
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

# iOS

Fully on-premise **Face Liveness SDK for iOS** (presentation-attack detection), with both **passive** scoring and a guided **active** challenge flow. The demo does not enroll people or run 1:N search.

| What                  | Id                                 |
| --------------------- | ---------------------------------- |
| iOS bundle identifier | `com.faceplugin.faceliveness.app`  |
| Xcode scheme          | `FaceLivenessDetectioniOS`         |
| Engine class          | `FaceRecognitionSDK`               |
| Kit entry point       | `FaceEngine.shared`                |

Keep the bundle id **`com.faceplugin.faceliveness.app`** so the sample license key keeps working.

{% hint style="warning" %}
**This build changed engines.** Earlier releases shipped `facelivenessdk.framework`, `FaceLivenessEngine.framework` and `onnxruntime.framework`. Those are retired. The SDK now runs on the Face Recognition engine bundles, and licences are checked by that runtime — a key issued for the old one will not activate. [Request a new key](../../../../help/request-a-license-and-support.md).
{% endhint %}

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceLivenessDetection-iOS" %}

### Setup <a href="#setup" id="setup"></a>

{% stepper %}
{% step %}
#### Get the engine bundles

Download the four bundles from the [iOS release](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-iOS/releases/tag/latest) and place them at the repository root, next to `FaceLivenessSDK.xcodeproj`:

`facerecognitionsdk.framework`, `FacePluginSDK.xcframework`, `FacePlugin.xcframework`, `FaceDetect.xcframework`

All four are **device only** (`ios-arm64`), which is why the app runs on a physical iPhone and not in the simulator.
{% endstep %}

{% step %}
#### Embed them

Add all four to the target with **Embed & Sign**.

Inside `FacePlugin.xcframework` the `ios-arm64` slice is named **`FacePPlugin.framework`**, with two Ps. That is deliberate — linker flags and any host that embeds the slice directly must use that name.
{% endstep %}

{% step %}
#### Add the kit

Add **FaceLivenessKit** for the engine lifecycle, and copy the `ActiveLiveness` group if you want the guided screen. Add `NSCameraUsageDescription` to `Info.plist`.
{% endstep %}

{% step %}
#### Start the engine

Activate once at launch. It runs off the main thread on its own:

```swift
FaceEngine.shared.start(license: "YOUR_LICENSE_KEY") { code in
    // code == 0 once the engine is ready
}
```
{% endstep %}
{% endstepper %}

Minimum deployment target **iOS 15.0**.

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

### Passive liveness

```swift
let faces = FaceEngine.shared.detect(image, options: .passive)
if let face = faces.first, face.liveness >= 0.5 {
    // Real
}
```

The liveness score ranges from 0.0 to 1.0. **0.5 or higher** is treated as Real. That is the demo default.

### Active liveness

One attempt runs in three stages: **align**, then a **passive anti-spoofing gate**, then a shuffled sequence of **challenges**. Below the gate threshold the attempt ends and **no challenge is asked** — a printed photo or a replayed video is never invited to blink. Losing the face for about a second restarts the whole attempt, gate included.

The screen is one view controller, presented with a completion:

```swift
ActiveLivenessViewController.present(from: self, options: ActiveLivenessOptions()) { result in
    guard let result else { return }          // closed before it finished
    if result.passed {
        // live person
    } else if !result.passivePassed {
        // turned away at the anti-spoofing gate
    } else {
        // a challenge was not completed: result.failedCheck
    }
}
```

`FaceEngine.shared.start` has to have succeeded first.

#### How the checks are judged

The sequence lives in `ActiveLivenessSession`, over the per-frame attributes the engine reports — the passive liveness score drives the gate, `Pan` / `Tilt` in degrees drive the turn and tilt challenges, eye openness drives blink, and the smile estimate drives smile.

| Challenge | Satisfied when |
| --------- | -------------- |
| Blink | Eye openness drops to the eye-closed threshold and recovers, **Blinks required** times |
| Smile | The smile estimate reaches the smile threshold |
| Turn left / right | Yaw passes the turn angle in that direction |
| Look up / down | Pitch passes the tilt angle in that direction |
| Move closer | The face box grows by the move-closer fraction from where the challenge started |

Each challenge must be held for two frames running, and fails if **frames allowed per stage** elapse first.

#### When a face cannot be checked

`ActiveLivenessSession.Notice` names what is wrong rather than leaving the user holding still against a frame that will never pass: move closer, move back a little, move your face into the middle, look straight at the camera, too dark or blurry, cannot read your face, only one face please. These apply while the face is being framed and scored — a challenge deliberately turns the head away, so the pose and quality checks step aside once the challenges start.

{% hint style="info" %}
**Head-pose direction.** Which way is positive for yaw and pitch is a property of the engine build. If a turn challenge accepts the opposite direction on your device, flip **Settings → Invert left / right** (or **Invert up / down**).
{% endhint %}

### Defaults

| Setting | Default |
| ------- | ------- |
| Challenges asked | **3**, drawn at random from a pool of 6 |
| Liveness threshold (the gate) | **0.5** |
| Passive samples averaged at the gate | **3** |
| Frames allowed per stage | **150** |
| Yaw / pitch | **25°** / **15°** |
| Smile / blink | **0.5** / **0.3**, **2** blinks required |
| Move-closer fraction | **0.15** |
| Align angle / minimum face fraction / quality | **12°** / **0.3** / **0.3** |

`ActiveLivenessResult` carries `passed`, `failure` (`none`, `spoof`, `check`, `timeout`, `cancelled`), `passiveScore`, `passivePassed`, `challenges`, `outcomes` and `durationMs`.

### Non-Swift hosts

Swift-only API — structs, enums with associated values — is invisible to Objective-C and therefore to anything that binds a framework through its generated header: .NET MAUI, Xamarin, Unity, Cordova's Objective-C plugin API.

**FaceLivenessBridge** is a framework target in the project whose only job is to put an `@objc` face on the same screens: `FPLEngine`, `FPLActiveOptions`, `FPLActiveResult`, `FPLLiveness`, all `NSObject`s and `Int`-backed enums. It adds no behaviour and compiles the *same source files* the app does, so the two cannot drift.

Both xcframeworks are published as `bridge.zip` on the [Releases page](https://github.com/Faceplugin-ltd/FaceLivenessDetection-iOS/releases/latest), so a host does not need a Mac to consume them. A host embeds `FaceLivenessBridge.xcframework` and `FaceLivenessKit.xcframework` along with the four engine bundles. **Device only** (`ios-arm64`).

To rebuild them yourself, on macOS with Xcode:

```bash
./scripts/build_bridge.sh            # → build/xcframeworks/
``` The wire values it reports — challenge keys, outcome and failure names — are the ones Android uses, so a cross-platform host can treat both platforms identically. The [.NET MAUI SDK](liveness-detection-dot-net-maui-sdk.md) is built on it.

Objective-C and Swift apps should use `FaceLivenessKit` and the screens directly; the bridge exists only for hosts that cannot read a Swift interface.

### Run the demo

1. Place the four engine bundles at the repository root (see Setup).
2. Open `FaceLivenessSDK.xcodeproj`, select scheme **FaceLivenessDetectioniOS**, set your **Team**.
3. Keep the bundle id **`com.faceplugin.faceliveness.app`**.
4. Prefer a **USB** iPhone — Wi‑Fi debugging often hangs LLDB. Or run `python3 run_ios`.
5. Home tiles: **Active liveness**, **Passive liveness**, Settings, About.

### Screenshots

The demo is the same four-screen app on every platform.

| Home | Active liveness | Active result | Passive liveness |
| ---- | --------------- | ------------- | ---------------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/home.gif" alt="Faceplugin Face Liveness home" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_start.gif" alt="Faceplugin Face Liveness guided active challenge sequence" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_result.png" alt="Faceplugin Face Liveness active result" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/passive.png" alt="Faceplugin Face Liveness passive camera anti-spoofing" data-size="original"> |

### License

Licenses are **offline** and bound to your bundle identifier. The sample key covers `com.faceplugin.faceliveness.app` only. After activation, `FaceEngine.shared.licenseStatus()` reports what the key unlocks — **Liveness only** and **Recognition + Liveness** both unlock the liveness screens.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Is the iOS SDK passive or active liveness detection?**

Both. It scores the face in a camera frame or image passively, and ships a guided active flow with an anti-spoofing gate and a shuffled challenge sequence.

**What liveness threshold should I use on iOS?**

Treat a score of 0.5 or higher as real. This is the demo default and the default gate threshold.

**Which frameworks do I need?**

`facerecognitionsdk.framework`, `FacePluginSDK.xcframework`, `FacePlugin.xcframework` and `FaceDetect.xcframework`, all added with Embed & Sign. The older `facelivenessdk` / `FaceLivenessEngine` / `onnxruntime` set is retired.

**Why does the app not run in the simulator?**

The engine ships no simulator slice. All four bundles are `ios-arm64` device only.

**Can I change the demo's bundle ID?**

Keep `com.faceplugin.faceliveness.app` to use the included license. If you change it, request a new license key.

**Does the iOS liveness SDK work offline?**

Yes. Activation and liveness detection run entirely on the iPhone.

### Related documentation

* [Faceplugin Face Liveness Detection Mobile SDK](./) · [Android](liveness-detection-android-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition iOS SDK](../../recognition-and-liveness/mobile-sdk/face-recognition-ios-sdk.md)
* [ID Document Recognition iOS SDK](../../../id-document-recognition-sdk/recognition-and-liveness/mobile-sdk/id-document-recognition-ios-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
