---
description: >-
  Faceplugin Face Liveness Detection .NET MAUI SDK. On-premise passive and
  active anti-spoofing from one C# codebase for Android and iOS.
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

# .NET MAUI

Fully on-premise **Face Liveness SDK for .NET MAUI**. One C# codebase targets `net8.0-android` and `net8.0-ios`, over the same native engine as every other platform.

This repository is an **app**, not a NuGet package. You clone it and build on it, or lift the two C# classes into your own MAUI project.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceLivenessDetection-DotNet-MAUI" %}

### How the platforms differ

| Platform | Active liveness | Passive liveness |
| -------- | --------------- | ---------------- |
| Android | The native `ActiveLivenessActivity` from `face-liveness-active.aar` | A C# screen over the native kit |
| iOS | `FPLLiveness.PresentActiveLiveness` from `FaceLivenessBridge.xcframework` | Native |

On iOS the Swift screens are reached through **FaceLivenessBridge**, an `@objc` shim built from the iOS repository. Swift-only API — structs and enums with associated values — is invisible to a .NET binding, so the bridge puts `NSObject`s and `Int`-backed enums on the same screens without adding behaviour. The wire values match Android, so your C# sees one model.

### How to run

{% stepper %}
{% step %}
#### Install tools

.NET **8** SDK (the repository pins `8.0.100` in `global.json`), the MAUI workloads, Android Studio and/or Xcode 15+. A physical phone — the engine ships no simulator slice.
{% endstep %}

{% step %}
#### Clone

```bash
git clone https://github.com/Faceplugin-ltd/FaceLivenessDetection-DotNet-MAUI.git
cd FaceLivenessDetection-DotNet-MAUI
```
{% endstep %}

{% step %}
#### Place the Android runtime

Three AARs go in `src/FaceLiveness.Net.Maui/Platforms/Android/libs/`:

| File | Where it comes from |
| ---- | ------------------- |
| `facerecognitionsdk.aar` | The [engine release](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Android/releases/tag/latest) |
| `face-liveness-kit.aar` | `liveness-libs.zip` on the [FaceLivenessDetection-Android release](https://github.com/Faceplugin-ltd/FaceLivenessDetection-Android/releases/latest) |
| `face-liveness-active.aar` | the same `liveness-libs.zip` |

All three are downloads. Unzip `liveness-libs.zip` and place both AARs beside the engine. Building them from source with `./gradlew :face-liveness-kit:assembleRelease :face-liveness-active:assembleRelease` stays available if you want to.
{% endstep %}

{% step %}
#### Place the iOS runtime

Six bundles go in `src/FaceLiveness.Net.Maui/Platforms/iOS/Frameworks/`:

* `FaceLivenessBridge.xcframework` and `FaceLivenessKit.xcframework` — `bridge.zip` on the [FaceLivenessDetection-iOS release](https://github.com/Faceplugin-ltd/FaceLivenessDetection-iOS/releases/latest)
* `facerecognitionsdk.framework`, `FacePluginSDK.xcframework`, `FacePlugin.xcframework`, `FaceDetect.xcframework` — `runtime.zip` on the [engine release](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-iOS/releases/tag/latest)

Unzip both archives into `Frameworks/`. Because the bridge is a download, **you do not need a Mac** to build this app — only to rebuild the bridge yourself with `./scripts/build_bridge.sh`.

All are device-only (`ios-arm64`). The build fails with a named message listing whichever is missing.
{% endstep %}

{% step %}
#### Build and run

```bash
python run_android.py        # or: python run_ios.py on macOS
```

That is the whole setup. The script restores, builds and installs onto the plugged-in phone — the equivalent of `dotnet build -t:Run -f net8.0-android`. Wait for the home status to read **Ready**.
{% endstep %}
{% endstepper %}

**Running it by hand.** The script is a convenience, not a requirement — the same sequence, with the runtime already in place:

```bash
dotnet build src/FaceLiveness.Net.Maui/FaceLiveness.Net.Maui.csproj -f net8.0-android -t:Run
dotnet build src/FaceLiveness.Net.Maui/FaceLiveness.Net.Maui.csproj -f net8.0-ios -t:Run     # macOS
```

Keep the demo ids so the included license works:

| Platform | Id |
| -------- | -- |
| Android application id | `com.faceplugin.faceliveness` |
| iOS bundle identifier | `com.faceplugin.faceliveness.app` |

Minimums: Android **API 24**, iOS **15.0**.

### Start the engine

```csharp
using FaceLiveness.Net.Maui;

var code = await FaceLivenessEngine.StartAsync(licenseKey);
if (code != SdkCode.Success)
{
    var reason = await FaceLivenessEngine.LastLicenseErrorAsync();
}

FaceLivenessEngine.StateChanged += state => { /* EngineState */ };
```

| Member | What it does |
| ------ | ------------ |
| `StartAsync(string license)` | Activates and initialises the engine, returns a status code |
| `State` / `IsReady` / `LastCode` | Current engine state |
| `StateChanged` | `event Action<EngineState>` |
| `RefreshLicenseAsync()` | Re-reads the `LicenseStatus` |
| `MachineCodeAsync()` | Machine code, to send when requesting a key |
| `LastLicenseErrorAsync()` / `StatusMessageAsync()` | Diagnostics for the UI |

`EngineState`: `Loading`, `Ready`, `NotActivated`, `LicenseInvalid`, `LicenseExpired`, `Failed`.
Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

### Passive liveness

```csharp
await FaceLivenessScreens.StartPassiveLivenessAsync(
    useFrontCamera: true,
    livenessThreshold: 0.5f);
```

**0.5 or higher** is treated as Real.

### Active liveness

```csharp
var result = await FaceLivenessScreens.StartActiveLivenessAsync(new ActiveLivenessOptions());

if (result is null)
{
    // closed before it finished
}
else if (result.Passed)
{
    // live person
}
else if (!result.PassivePassed)
{
    // turned away at the anti-spoofing gate
}
else
{
    var failed = result.FailedCheck;
}
```

The attempt runs **align → passive anti-spoofing gate → shuffled challenges**. Below the gate threshold it ends there and no challenge is asked. Losing the face for about a second restarts the whole attempt, gate included.

`ActiveLivenessResult` is a record: `Passed`, `Failure` (`ActiveLivenessFailure.None` / `.Spoof` / `.Check` / `.Timeout` / `.Cancelled`), `PassiveScore`, `PassivePassed`, `Challenges`, `Outcomes` (`StepState.Pending` / `.Active` / `.Passed` / `.Failed`), `DurationMs`, plus the computed `FailedCheck`.

`ActiveCheckKey`: `Blink`, `Smile`, `TurnLeft`, `TurnRight`, `TurnUp`, `TurnDown`, `Perspective`. `ActiveChecks.DefaultPool` is the first six; `ActiveChecks.Info(key)` gives the title and hint, and `ActiveChecks.FromWire(string)` maps the shared wire keys (`turn_left`, …).

`LivenessText` provides `MessageForCode`, `StepLabel` and `Describe(result)` for building the result UI.

### Defaults

`ActiveLivenessOptions` ships the same defaults as every other platform: `Count` **3**, `Randomize` **true**, `LivenessThreshold` **0.5f**, `PassiveSamples` **3**, `FramesWait` **150**, `YawThreshold` **25f**, `PitchThreshold` **15f**, `SmileThreshold` **0.5f**, `BlinkThreshold` **0.3f**, `BlinksNumber` **2**, `PerspectiveThreshold` **0.15f**, `AlignAngle` **12f**, `MinFaceFraction` **0.3f**, `QualityThreshold` **0.3f**, `InvertTurn` / `InvertTilt` **false**, `UseFrontCamera` **true**.

{% hint style="info" %}
**Head-pose direction.** Which way is positive for yaw and pitch is a property of the engine build. If a turn challenge accepts the opposite direction on your device, set `InvertTurn` (or `InvertTilt`).
{% endhint %}

### Integrate into your own app

There is no NuGet package. Take `FaceLiveness.cs` and `LivenessModels.cs` plus the `Platforms/` partials into your MAUI project, add the `FaceLivenessBridge.Binding` project for iOS, and place the runtimes in the same paths. Set **your** `ApplicationId` and bundle identifier, add the camera permission, and request a license for those ids.

### Screenshots

| Home | Active liveness | Active result | Passive liveness |
| ---- | --------------- | ------------- | ---------------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/home.gif" alt="Faceplugin Face Liveness .NET MAUI home" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_start.gif" alt="Faceplugin Face Liveness guided active challenge sequence" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/active_result.png" alt="Faceplugin Face Liveness active result" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/passive.png" alt="Faceplugin Face Liveness passive camera anti-spoofing" data-size="original"> |

### License

Licenses are **offline** and bound to your application id and bundle identifier. The sample key covers the demo ids only. [Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md).

### Frequently Asked Questions

**Is there a NuGet package?**

No. The repository is a MAUI app you clone and build on, or lift the C# classes from.

**Why does it need six bundles on iOS?**

Four are the engine. The other two — `FaceLivenessBridge` and `FaceLivenessKit` — are the `@objc` shim and the kit, built from the iOS repository, because .NET cannot bind a Swift-only interface.

**Why is Android passive liveness a C# screen but iOS native?**

The Android kit exposes per-frame attributes cleanly to C#, so the passive screen is a thin C# port. On iOS the screen comes through the bridge. Both score identically — it is the same engine.

**Can I run it in a simulator?**

No. The engine is device-only (`ios-arm64`, `arm64-v8a` / `armeabi-v7a`).

**Does it work offline?**

Yes. Once `StartAsync` succeeds with a key for your ids, everything runs on the device.

### Related documentation

* [Faceplugin Face Liveness Detection Mobile SDK](./) · [iOS](liveness-detection-ios-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition .NET MAUI SDK](../../recognition-and-liveness/mobile-sdk/face-recognition-dot-net-maui-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
