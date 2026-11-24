---
description: >-
  Faceplugin Face Recognition .NET MAUI SDK. Fully on-premise face matching, 1:N
  identify and 2D liveness for Android and iOS from one C# codebase.
icon: code
---

# .NET MAUI

Fully on-premise **Face Recognition + Liveness for .NET MAUI**. One C# codebase targets `net8.0-android` and `net8.0-ios`, and calls the same native engine as the Android and iOS apps, so scores match on both platforms. Images never leave the device.

Entry point: `FaceRecognition.Net.Maui.FaceSdk`. The demo app has five tabs: Identity, People, Attribute, Liveness and Settings.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-DotNet-MAUI" %}

### How to run

#### Install tools

```bash
# .NET 8 SDK, then:
dotnet workload install maui
```

Android needs Android Studio and a USB phone with debugging on. iOS needs macOS with Xcode 15+ and a signing team. `global.json` keeps the build on .NET 8 when a newer SDK is the default.

#### Place the native runtimes

The native binaries are too large for GitHub, so they come from the Android and iOS release pages. Take both from the same release.

| Platform | Download | Place at |
| -------- | -------- | -------- |
| Android | [Android release](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Android/releases/tag/latest) | `src/FaceRecognition.Net.Maui/Platforms/Android/libs/facerecognitionsdk.aar` |
| iOS | [iOS release](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-iOS/releases/tag/latest) | `src/FaceRecognition.Net.Maui/Platforms/iOS/Frameworks/` — `facerecognitionsdk.framework`, `FacePluginSDK.framework`, `FacePPlugin.framework`, `FaceDetect.framework` |

The project references the AAR, and compiles `FaceBridge.java`, only when that file is present. Each iOS framework is linked only when the folder holds it. Device arm64 only.

#### Run on a phone

```bash
git clone https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-DotNet-MAUI.git
cd FaceRecognition-LivenessDetection-DotNet-MAUI
# place the AAR and the iOS frameworks first
python run_android
python run_ios          # macOS
```

The run scripts are the same as:

```bash
dotnet build src/FaceRecognition.Net.Maui/FaceRecognition.Net.Maui.csproj -f net8.0-android -t:Run
dotnet build src/FaceRecognition.Net.Maui/FaceRecognition.Net.Maui.csproj -f net8.0-ios -r ios-arm64 -t:Run
```

Keep the demo identifiers so the bundled license activates: Android application id `com.faceplugin.facerecognitionsdk`, iOS bundle id `com.faceplugin.facerecognition.app`.

| Tab | What it does |
| --- | ------------ |
| **Identity** | Live 1:N against everyone enrolled, with passive 2D liveness, then a ranked result screen |
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

### License

Licenses are **offline** and bound to your application id / bundle id. The demo ships with keys for the two demo identifiers above, in `src/FaceRecognition.Net.Maui/LicenseKeys.cs`. For your own app, [request a license](../../../../help/request-a-license-and-support.md) for the ids you will ship.

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed. `FaceSdk.StatusLabel(code)` turns a code into a message.

### Integrate into your own app

You integrate from the repository itself. There is no NuGet package and no package-server dependency.

1. Clone the repository and add `src/FaceRecognition.Net.Maui` to your solution, or copy `FaceSdk.cs` plus the platform file for the target you ship.
2. Android: add `facerecognitionsdk.aar` as an `AndroidLibrary` and keep `FaceBridge.java` on the compile classpath.
3. iOS: add the four frameworks as `NativeReference` items with `ForceLoad`.
4. Set **your** application ids and request a license for them.
5. Keep engine calls off the UI thread — `SdkGate` already serializes them.
6. Add camera and photo-library permissions on both platforms.

You do not need the demo pages. `SdkSession.BootstrapAsync()` shows the whole activation sequence, and publishes `Ready`, `Loading` and `Message` for your UI.

### APIs

All calls are `async` and return a status code, JSON, or a value. `0` means success.

#### <mark style="color:orange;">GetMachineCodeAsync:</mark> This API returns the machine code for a license request

```csharp
public static partial Task<string> GetMachineCodeAsync();
```

#### <mark style="color:orange;">SetActivationAsync:</mark> This API activates the SDK

```csharp
public static partial Task<int> SetActivationAsync(string license);
```

| **Input** | **license** (string): the `FP1.` license key, bound to your application id / bundle id |
| --------- | -------------------------------------------------------------------------------------- |
| **Return value** | Status code. **0** Success, **1** Invalid, **2** Expired, **3** Not activated, **4** Init failed |

#### <mark style="color:orange;">InitAsync:</mark> This API initializes the engine

```csharp
public static partial Task<int> InitAsync();
```

#### <mark style="color:orange;">GetLicenseStatusAsync:</mark> This API reads what the key unlocks

```csharp
public static partial Task<string> GetLicenseStatusAsync();
public static partial Task<string> LastLicenseErrorAsync();
```

#### <mark style="color:orange;">DetectAsync:</mark> This API detects faces and returns engine JSON

```csharp
public static partial Task<string> DetectAsync(byte[] jpeg, int flags);
public static partial Task<string> DetectWithTemplatesAsync(byte[] jpeg, int flags);
```

| **Input** | **jpeg** (byte[]): the image · **flags** (int): which attributes to compute, including liveness |
| --------- | ----------------------------------------------------------------------------------------------- |
| **Return value** | JSON with the face boxes, pose, attributes, liveness, and templates for `DetectWithTemplatesAsync` |

#### <mark style="color:orange;">SimilarityAsync:</mark> This API compares two face templates

```csharp
public static partial Task<double> SimilarityAsync(string feature1B64, string feature2B64);
public static partial Task<double[]> SimilarityManyAsync(string probeB64, IReadOnlyList<string> galleryB64);
```

| **Return value** | A similarity score. The demo Identify threshold is **0.67** |
| ---------------- | ----------------------------------------------------------- |

#### <mark style="color:orange;">CropFaceAsync:</mark> This API returns the cropped face for enrollment

```csharp
public static partial Task<byte[]?> CropFaceAsync(byte[] jpeg, FaceBox box);
```

#### <mark style="color:orange;">VideoWorker:</mark> These APIs run live 1:N identify

```csharp
await FaceSdk.StartVideoWorkerAsync(0.67f);
await FaceSdk.SyncDatabaseAsync(features, 0.67f);
await FaceSdk.AddFrameAsync(jpeg);
var evt = await FaceSdk.PollEventAsync();
await FaceSdk.StopVideoWorkerAsync();
```

Live camera frames also go through `ApplyLiveFrameAsync` / `IngestLiveCameraFrameAsync`, which prepare the frame and feed the worker in one call.

{% hint style="warning" %}
Keep every engine call off the UI thread. The engine is not concurrent — `SdkGate` serializes the calls for you.
{% endhint %}

### System requirements

| Item | Minimum | Recommended |
| ---- | ------- | ----------- |
| Android | API 24 (7.0), `arm64-v8a` | Android 10 or newer |
| iOS | 15.0, arm64 device | iPhone 11 or newer |
| .NET | 8.0 with the MAUI workload | 8.0, latest patch |
| RAM (device) | 4 GB | 6 GB or more |
| Device | Physical phone | Same; the simulator has no usable camera |

### Frequently Asked Questions

**Is there a NuGet package?**

No. The SDK ships as this repository plus the engine binaries on the Android and iOS release pages. Add the project to your solution instead of a package reference.

**Does it work offline?**

Yes. Once activated with a key bound to your application id, everything runs on the device.

**Which platforms does the MAUI SDK target?**

`net8.0-android` and `net8.0-ios`, on physical arm64 devices.

**What is the default match threshold?**

0.67 for Identify, with a 0.5 liveness threshold, the same defaults as the native apps.

### Related documentation

* [Faceplugin Face Recognition Mobile SDK](./) · [Android](face-recognition-android-sdk.md) · [iOS](face-recognition-ios-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
