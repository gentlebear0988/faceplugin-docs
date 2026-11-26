---
description: >-
  Faceplugin Face Liveness Detection Android SDK. On-premise passive and active
  PAD against photos, screens, masks, and deepfakes.
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

Fully on-premise **Face Liveness SDK for Android** (presentation-attack detection), with both **passive** scoring and a guided **active** challenge flow. The demo does not enroll people or run 1:N search.

| What                      | Id                                                     |
| ------------------------- | ------------------------------------------------------ |
| Android demo package name | `com.faceplugin.faceliveness`                          |
| Public Java class         | `com.faceplugin.facerecognitionsdk.FaceRecognitionSDK` |
| Engine binary             | `libs/facerecognitionsdk.aar`                          |

Keep the demo package name **`com.faceplugin.faceliveness`** so the sample license key keeps working.

{% hint style="warning" %}
**This build changed engines.** Earlier releases shipped `facelivenessdk.aar` with a `FaceLivenessSDK` class. That engine is retired. The SDK now runs on `facerecognitionsdk.aar` — the same engine build the Face Recognition product uses — and licences are checked by that runtime. A key issued for the old runtime will report **Invalid license!** or **No activated!** on a clean install; [request a new one](../../../../help/request-a-license-and-support.md).
{% endhint %}

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceLivenessDetection-Android" %}

### What is in the repository

| Module | What it is |
| ------ | ---------- |
| `face-liveness-kit` | Engine lifecycle, serialized SDK calls, camera frames, per-frame face attributes |
| `face-liveness-active` | The guided active-liveness screen: anti-spoofing gate, then the challenges, returned as a result |
| `app` | The demo, built from those two libraries |
| `libs/` | `facerecognitionsdk.aar`, the engine |

Both libraries are also published to Maven Central as `com.faceplugin:face-liveness-kit` and `com.faceplugin:face-liveness-active`.

### Setup <a href="#setup" id="setup"></a>

{% stepper %}
{% step %}
#### Get the engine

`libs/facerecognitionsdk.aar` is empty on GitHub because the binary is too large, and Gradle fails fast when it is missing. Download it from the [Android release](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Android/releases/tag/latest) and place it in `libs/` — not in a nested folder.
{% endstep %}

{% step %}
#### Add the modules

Copy `face-liveness-kit/` and `face-liveness-active/` into your project, then in `settings.gradle`:

```
include ':face-liveness-kit'
include ':face-liveness-active'
```
{% endstep %}

{% step %}
#### Declare the dependencies

In `app/build.gradle`:

```gradle
implementation project(':face-liveness-active')
implementation files(rootProject.file('libs/facerecognitionsdk.aar'))
```

with `minSdk 24`, `abiFilters 'arm64-v8a', 'armeabi-v7a'`, and `packaging { jniLibs { useLegacyPackaging = true } }`.
{% endstep %}

{% step %}
#### Start the engine

Add the `CAMERA` permission, then activate once at start-up. `FaceEngine.start` does the activation and the init for you, off the main thread:

```kotlin
FaceEngine.start(context, "YOUR_LICENSE_KEY") { code ->
    // code == FaceRecognitionSDK.SDK_SUCCESS once the engine is ready
}
```
{% endstep %}
{% endstepper %}

The engine is a large binary, so use [ABI splits or an app bundle](https://developer.android.com/studio/build/configure-apk-splits) for release builds.

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

### Passive liveness

Score a frame and read the result. Nothing is asked of the user.

```kotlin
val faces = FaceEngine.detect(bitmap, DetectOptions.PASSIVE)
if (faces.isNotEmpty() && faces[0].liveness >= 0.5f) {
    // Real
}
```

The liveness score ranges from 0.0 to 1.0. **0.5 or higher** is treated as Real. That is the demo default.

### Active liveness

One attempt runs in three stages, in this order:

1. **Align** — one face, framed well enough to be scored.
2. **Passive anti-spoofing gate** — the face is scored and a few frames are averaged. Below the threshold the attempt ends here and **no challenge is asked**: a printed photo or a replayed video is never invited to blink.
3. **Challenges** — a shuffled sequence of actions over the face frame, with a progress ring and per-step instructions.

It ends on a result card listing the gate and every challenge with its outcome, how long the attempt took, and the passive score the gate measured.

**Losing the face starts the attempt over.** Gone from the frame for about a second and everything resets to stage one, the gate included, so no attempt can be half-completed by one face and finished by another. `ActiveLivenessResult` is only ever produced for an attempt run end to end by one face.

The screen is one activity, launched for a result:

```kotlin
val launcher = registerForActivityResult(StartActivityForResult()) { outcome ->
    val result = ActiveLivenessActivity.result(outcome.data)
    when {
        result == null -> { /* closed before it finished */ }
        result.passed -> { /* live person */ }
        !result.passivePassed -> { /* turned away at the anti-spoofing gate */ }
        else -> { /* a challenge was not completed: result.failedCheck */ }
    }
}

launcher.launch(
    ActiveLivenessActivity.intent(
        this,
        ActiveLivenessOptions(count = 3, livenessThreshold = 0.5f),
    ),
)
```

`FaceEngine.start` has to have succeeded first.

#### How the checks are judged

The engine scores a frame; it does not sequence anything. The sequence lives in `ActiveLivenessSession`, over the per-frame attributes the engine reports:

```kotlin
val options = FaceRecognitionSDK.DetectOptions()
options.liveness = true    // passive anti-spoofing score -> the gate
options.pose = true        // yaw / pitch / roll, in degrees -> the turn and tilt challenges
options.eyes = true        // eyesOpen 0..1               -> blink
options.expression = true  // smile, mouthOpen 0..1       -> smile
val faces = FaceRecognitionSDK.detect(bitmap, options)
```

| Stage | What it waits for |
| ----- | ----------------- |
| Align | One face, framed and square to the camera |
| Gate | The average of **passive samples** scores reaching the **score threshold** |
| Each challenge | The condition below, held for two frames running |
| Restart | The face out of frame for about a second, at any stage past aligning |
| Fail | **Frames allowed per stage** elapse without the condition being met |

| Challenge | Satisfied when |
| --------- | -------------- |
| Blink | `eyesOpen` drops to the eye-closed threshold and recovers, **Blinks required** times |
| Smile | `smile` reaches the smile threshold |
| Turn left / right | `yaw` passes the turn angle in that direction |
| Look up / down | `pitch` passes the tilt angle in that direction |
| Move closer | The face box grows by the move-closer fraction from where the challenge started |

Turning the wrong way is answered with a nudge rather than a silent failure.

#### When a face cannot be checked

Rather than leaving the user holding still against a frame that will never pass, the screen names what is wrong:

| On screen | What the frame shows |
| --------- | -------------------- |
| Move closer | The face fills less of the frame than **Minimum face size** |
| Move back a little | The face runs past the edge of the frame, or nearly fills it |
| Move your face into the middle | The face is well off centre |
| Look straight at the camera | Yaw, pitch or roll is beyond **Start alignment tolerance** |
| Too dark or blurry | The engine's own `ImageQuality` is below **Minimum image quality** |
| Cannot read your face | Well framed, and the engine still returned no liveness score |
| Only one face, please | More than one face is in the frame |

These are checked in the order a person would fix them, and only while the face is being framed and scored — a challenge deliberately turns the head away, so the pose and quality checks step aside once the challenges start.

{% hint style="info" %}
**Head-pose direction.** `yaw` and `pitch` are the engine's `Pan` and `Tilt` in degrees, and which way is positive is a property of the engine build. If a turn challenge accepts the opposite direction on your device, flip **Settings → Invert left / right** (or **Invert up / down**) — no rebuild needed.
{% endhint %}

#### Replay resistance

The gate runs before the challenges, and the sequence is shuffled on each attempt, so the challenges and their order are not known ahead of time. Keep both on in production: the result reports the passive score and each challenge separately.

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

`ActiveLivenessResult` carries `passed`, `failure` (`NONE`, `SPOOF`, `CHECK`, `TIMEOUT`, `CANCELLED`), `passiveScore`, `passivePassed`, `challenges`, `outcomes` (`PENDING`, `ACTIVE`, `PASSED`, `FAILED`) and `durationMs`.

### Run the demo

1. Place `facerecognitionsdk.aar` in `libs/` (see Setup).
2. Keep the demo package name **`com.faceplugin.faceliveness`**.
3. Run on a **physical** phone.
4. Home tiles: **Active liveness**, **Passive liveness**, Settings, About. The demo does **not** enroll people or run 1:N search.

### Screenshots

| Home | Active liveness | Active result | Passive liveness |
| ---- | --------------- | ------------- | ---------------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/home.gif" alt="Faceplugin Face Liveness Android home" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_start.gif" alt="Faceplugin Face Liveness guided active challenge sequence" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_result.png" alt="Faceplugin Face Liveness active result: gate and challenges passed" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/passive.png" alt="Faceplugin Face Liveness passive camera anti-spoofing" data-size="original"> |

### License

Licenses are **offline** and bound to your `applicationId`. The engine reads the key against your package name, so a key issued for one `applicationId` does not activate another. The demo ships the key for `com.faceplugin.faceliveness`.

After activation, `FaceRecognitionSDK.license()` (and `FaceEngine.licenseStatus()` in the demo kit) reports what the key unlocks:

* **Liveness only** / **Recognition + Liveness** — both liveness tiles unlock
* **Recognition only** — liveness stays unavailable in this app
* **Not licensed** — the tiles stay locked until you activate

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Is this passive or active liveness detection?**

Both. The SDK scores a frame passively, and ships a guided active flow that gates on the passive score and then asks for a shuffled sequence of actions.

**What liveness threshold should I use on Android?**

Treat 0.5 or higher as a pass, unless Faceplugin support recommends a different value for your use case. The same default gates the active flow.

**Which AAR do I need?**

`facerecognitionsdk.aar`, from the Face Recognition Android release. The older `facelivenessdk.aar` is retired and its licence keys do not work on this runtime.

**What is the minimum Android version?**

The SDK requires minSdk 24, with `arm64-v8a` or `armeabi-v7a`.

**Can I change the demo's package name?**

Keep `com.faceplugin.faceliveness` to use the sample license key. For your own package name, request a license.

**Does the Android liveness SDK work offline?**

Yes. Activation and liveness detection run entirely on the device.

### Related documentation

* [Faceplugin Face Liveness Detection Mobile SDK](./) · [iOS](liveness-detection-ios-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition Android SDK](../../recognition-and-liveness/mobile-sdk/face-recognition-android-sdk.md)
* [ID Document Recognition Android SDK](../../../id-document-recognition-sdk/recognition-and-liveness/mobile-sdk/id-document-recognition-android-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
