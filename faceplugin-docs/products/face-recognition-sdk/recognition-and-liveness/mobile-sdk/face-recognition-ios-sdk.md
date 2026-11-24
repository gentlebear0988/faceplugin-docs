---
description: >-
  Faceplugin Face Recognition iOS SDK. Fully on-premise face matching, 1:N
  identify, and 2D liveness. Xcode frameworks, setActivation, initSDK,
  detectImage, VideoWorker.
icon: apple
---

# iOS

Fully on-premise **Face Recognition SDK for iOS**. Detect faces, extract templates, match 1:1 or live 1:N, and check passive 2D liveness on the device.

You integrate through **`FaceRecognitionKit`**: `FaceEngine` for the lifecycle, `FaceGallery` for enrol and 1:N identify, `FaceDetector` for a single image, `FaceLimits` for the thresholds, and `CameraFrameUtils` for camera frames. `FaceRecognitionCapture` adds the guided capture screen. The demo app has five tabs — Identity, People, Attribute, Liveness and Settings — and is built on exactly these libraries.

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-iOS" %}

### Setup <a href="#setup" id="setup"></a>

You integrate from the repository itself. There is no CocoaPods spec and no package-server dependency.

1. Clone the repository. `Package.swift` resolves `facerecognitionsdk.xcframework` from the [GitHub release](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-iOS/releases/tag/latest), so a normal build needs no manual step. To build against an engine you already have on disk, put it at `Frameworks/facerecognitionsdk.xcframework` and set `FACEPLUGIN_LOCAL_ENGINE=1`.
2. In Xcode: **File → Add Package Dependencies → Add Local...**, choose the cloned folder, and add **FaceRecognitionKit** (and **FaceRecognitionCapture** for the capture coach) to your app target.
3. Add `NSCameraUsageDescription` and `NSPhotoLibraryUsageDescription` to `Info.plist`.
4. Set **your** Signing Team. Demo bundle id: **`com.faceplugin.facerecognition.app`**. Physical iPhone, iOS 15+. Start the engine once at launch; the kit keeps every engine call on its own queue.

### Quick start

Everything below is the **kit** API — `FaceRecognitionKit`, the Swift package the demo app is built on. It owns the engine lifecycle, keeps every native call on one queue, and stores the enrolled people for you. The raw engine is underneath it, documented in [Engine API reference](#engine-api-reference).

#### 1. Start the engine once at launch

```swift
import FaceRecognitionKit

func application(_ application: UIApplication,
                 didFinishLaunchingWithOptions options: [UIApplication.LaunchOptionsKey: Any]? = nil) -> Bool {
    FaceEngine.shared.start(license: Secrets.facepluginLicense) { code in
        // code == 0 when the engine is ready
    }
    return true
}
```

Watch the state, and keep the camera disabled until it is ready:

```swift
FaceEngine.shared.addListener { state in
    switch state {
    case .ready:   enableCamera()
    case .loading: showSpinner()
    default:       showLicenseError(FaceEngine.shared.stateMessage(state))
    }
}

FaceEngine.shared.isReady
FaceEngine.shared.licenseStatus()   // what the key unlocks
FaceEngine.shared.machineCode()     // send this when you request a key
```

#### 2. Set your thresholds once

```swift
let limits = FaceLimits(match: 0.80, liveness: 0.50, yaw: 20, roll: 20, pitch: 20)

limits.isLive(face.liveness)
limits.poseOk(face.yaw, face.roll, face.pitch)
limits.matches(similarity)
```

`FaceLimits.default` is the engine's operating point. Pass one instance to enrol, identify and the capture screen so they cannot disagree.

#### 3. Enrol a person

```swift
switch FaceGallery.shared.enroll(name: "Mina Ortiz", note: "Front desk", photo: photo, limits: limits) {
case let .enrolled(record, report, duplicates):
    person = record                       // id, name, note, photo
    for dup in duplicates {               // everyone already enrolled who matches
        print("also matches \(dup.name) at \(dup.similarity)")
    }
    log(report)                           // every engine call, and how long each took
case let .rejected(reason, _):
    toast(reason)                         // no face, bad pose, no template…
}
```

`duplicates` is the **full** list at or above the match threshold, highest first, not just the closest.

#### 4. Identify against everyone enrolled (1:N)

```swift
switch FaceGallery.shared.identify(probe, limits: limits, livenessOn: true) {
case let .done(result):
    for face in result.faces {
        if let best = face.best, face.matched(result.threshold) {
            show("\(best.name) at \(best.similarity)")
        } else {
            show("No match")
        }
    }
    print(result.extractMs, result.searchMs, result.gallerySize)
case let .rejected(reason, _):
    toast(reason)                         // no face, liveness failed, no template
}
```

With `livenessOn: true` a spoofed face is rejected **before** the gallery is searched.

#### 5. Read a single face, without the gallery

```swift
let prepared = CameraFrameUtils.enginePreparedImage(image)   // upright, long side 1280
let faces = FaceDetector.detect(prepared, param: .allAttributes())
guard let face = faces.first else { return }

face.liveness; face.faceQuality
face.yaw; face.roll; face.pitch

let template = FaceDetector.template(prepared, face: face)   // Data, store it yourself
let score = FaceDetector.similarity(templateA, templateB)    // 1:1
```

Feed **upright, unmirrored** frames to the detector and mirror only the overlay. `CameraFrameUtils` has the helpers: `frame(from:frontCamera:connection:)` for a `CMSampleBuffer`, `liveEngineImage` at about 640 px for a live feed, and `enginePreparedImage` at 1280 px for a still.

#### 6. What actually happened

`enroll`, `identify` and `attributes` return a `CallReport`:

```swift
report.success
report.totalMs
for step in report.steps { print(step.name, step.ms, step.ok, step.detail) }
```

{% hint style="info" %}
Call the kit from any thread: `SDKQueue` serializes every engine call for you. The first `start` unpacks the on-device models, so expect a few seconds before the state turns `.ready`.
{% endhint %}

### Engine API reference

Below is the **raw engine**, `FaceRecognitionSDK` from `facerecognitionsdk.xcframework`, which the kit wraps. Use it only when you are replacing the kit with your own camera pipeline and person database.


#### <mark style="color:orange;">setActivation:</mark> This API is used to activate the SDK <a href="#setactivation" id="setactivation"></a>

```objectivec
+(int)setActivation:(NSString*)license;
```

| **Input**        | <ul><li><strong>license</strong> (NSString*): The license string (<code>license key</code>)</li></ul>                                                            |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Return value** | <p>The SDK activation status code.</p><ul><li>0: Success</li><li>1: Invalid license</li><li>2: Expired</li><li>3: Not activated</li><li>4: Init failed</li></ul> |

#### <mark style="color:orange;">initSDK:</mark> This API is used to initialize the SDK <a href="#initsdk" id="initsdk"></a>

```objectivec
+(int)initSDK;
```

| **Input**        | None                                                                                                               |
| ---------------- | ------------------------------------------------------------------------------------------------------------------ |
| **Return value** | <p>The SDK initialization status code.</p><ul><li>0: Success</li><li>1–4: see <code>setActivation</code></li></ul> |

#### <mark style="color:orange;">getMachineCode:</mark> This API is used to retrieve the machine code <a href="#getmachinecode" id="getmachinecode"></a>

```objectivec
+(NSString*)getMachineCode;
```

#### <mark style="color:orange;">getLicenseStatus:</mark> This API is used to read license status <a href="#getlicensestatus" id="getlicensestatus"></a>

```objectivec
+(NSString*)getLicenseStatus;
+(BOOL)allowsRecognition;
+(BOOL)allowsLiveness;
+(NSString*)lastLicenseError;
+(BOOL)isActivated;
```

#### <mark style="color:orange;">detectImage:</mark> This API is used to detect faces and return engine JSON <a href="#detectimage" id="detectimage"></a>

```objectivec
+(NSString*)detectImage:(UIImage*)image crop:(BOOL)crop;
+(NSString*)detectImage:(UIImage*)image crop:(BOOL)crop flags:(FaceRecognitionDetectFlags)flags;
```

| **Input**        | <ul><li><strong>image</strong> (UIImage*): The input image</li><li><strong>flags</strong>: Estimators to run (<code>FaceRecognitionDetectAll</code>, <code>DetectLiveness</code>, …)</li></ul> |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Return value** | JSON string (Linux-compatible, same idea as HTTP `POST /api/detect`).                                                                                                                          |

#### <mark style="color:orange;">qualityImage:</mark> This API is used to run face image quality checks <a href="#qualityimage" id="qualityimage"></a>

```objectivec
+(NSString*)qualityImage:(UIImage*)image crop:(BOOL)crop;
```

#### <mark style="color:orange;">matchImage1:</mark> This API is used to compare two photos (1:1) <a href="#matchimage" id="matchimage"></a>

```objectivec
+(NSString*)matchImage1:(UIImage*)image1 image2:(UIImage*)image2 crop:(BOOL)crop;
```

#### <mark style="color:orange;">extractFeatureFromImage:</mark> This API is used to extract a face template <a href="#extractfeature" id="extractfeature"></a>

```objectivec
+(NSString*)extractFeatureFromImage:(UIImage*)image;
```

| **Input**        | A face image (typically cropped).                                     |
| ---------------- | --------------------------------------------------------------------- |
| **Return value** | JSON with the template. Store the feature bytes in **your** database. |

#### <mark style="color:orange;">similarityWithFeature1:</mark> This API is used to calculate the similarity between two face templates <a href="#similarity" id="similarity"></a>

```objectivec
+(float)similarityWithFeature1:(NSData*)feature1 feature2:(NSData*)feature2;
```

| **Input**        | Two template blobs from `extractFeatureFromImage`.                                       |
| ---------------- | ---------------------------------------------------------------------------------------- |
| **Return value** | Similarity **0.0–1.0**, or **-1** on error. Demo Identify default threshold is **0.67**. |

#### <mark style="color:orange;">setLandmarkMode:</mark> This API is used to set the landmark model <a href="#setlandmarkmode" id="setlandmarkmode"></a>

```objectivec
+(int)setLandmarkMode:(int)mode; // 14, 68, or 468
+(int)landmarkMode;
```

#### <mark style="color:orange;">VideoWorker:</mark> This API is used for live 1:N identify <a href="#videoworker" id="videoworker"></a>

```objectivec
+(int)startVideoWorkerWithMatchThreshold:(float)threshold;
+(int)startVideoWorkerWithConfig:(FaceRecognitionVideoWorkerConfig*)config;
+(int)syncVideoWorkerDatabaseWithFeatures:(NSArray<NSData*>*)features matchThreshold:(float)threshold;
+(int)addVideoWorkerFrame:(UIImage*)image;
+(int)addVideoWorkerSampleBuffer:(CMSampleBufferRef)sampleBuffer;
+(NSString*)extractFeatureWithSampleBuffer:(CMSampleBufferRef)sampleBuffer;
+(void)setVideoWorkerEventHandler:(void(^)(NSString* json))handler;
+(void)stopVideoWorker;
```

#### <mark style="color:orange;">deinitSDK:</mark> This API is used to unload the engine <a href="#deinitsdk" id="deinitsdk"></a>

```objectivec
+(int)deinitSDK;
```

### Run the demo

1. Place the three frameworks next to the Xcode project (see Setup).
2. Keep demo bundle id **`com.faceplugin.facerecognitionsdk.app`**.
3. Run on a **physical** iPhone (iOS 13+).
4. Wait until the home status bar shows Ready. Then the Identity, People, Attribute and Liveness tabs unlock.

Call **setActivation → initSDK** off the main thread. The engine is **not** concurrent.

### Screenshots

The demo is the same five-tab app on every platform.

| Identify | People | Attribute |
| -------- | ------ | --------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/identify.gif" alt="Faceplugin Face Recognition live 1:N identify with 2D liveness" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/people.gif" alt="Faceplugin Face Recognition enrolling people with the capture coach" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/attribute.gif" alt="Faceplugin Face Recognition face attributes, quality and landmarks" data-size="original"> |

| Liveness | Settings |
| -------- | -------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/liveness.gif" alt="Faceplugin passive 2D liveness check on the camera feed" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/settings.png" alt="Faceplugin Face Recognition match, liveness and pose thresholds" data-size="original"> |
### License

Licenses are **offline**. The sample key is only for the demo bundle. Request a new license key if you change the demo bundle.

Identify default threshold is **0.67**. Liveness demo default is **0.5**.

### Integrate into your own app

You need the three frameworks and `FaceRecognitionSDK`. You do **not** need the demo view controllers.

Typical call order: `setActivation` → `initSDK` → `detectImage` / `extractFeatureFromImage` → store templates in **your** database → `similarityWithFeature1:feature2:` or VideoWorker for live 1:N.

Public header only: `detectImage`, `extractFeatureFromImage`, `similarityWithFeature1:feature2:`, VideoWorker. There is **no** Objective-C `faceDetection:` returning `FaceBox` on this framework.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Which APIs does the iOS face recognition SDK provide?**

Activation, initialization, face detection, quality checks, 1:1 photo match, template extraction, template similarity and VideoWorker for live 1:N Identify. The API is Objective-C and is called from Swift through the bridging header.

**Does the iOS face recognition SDK work offline?**

Yes. Once activated with a license key for the demo bundle ID or your own, everything runs on the iPhone.

**What is the default face match threshold on iOS?**

The demo's Identify threshold is 0.67, and the liveness default is 0.5.

**Which iOS version is required?**

iOS 13 or later, on a physical iPhone.

**Which frameworks do I need?**

`facerecognitionsdk.framework`, `FaceRecognitionEngine.framework` and `onnxruntime.framework`, all added with Embed & Sign.

**Are the screenshots from iOS?**

No. They show the shared sample UI captured on Android; the iOS demo screens match them.

### Related documentation

* [Faceplugin Face Recognition Mobile SDK](./) · [Android](face-recognition-android-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Liveness Detection iOS SDK](../../liveness-detection-sdk/mobile-sdk/liveness-detection-ios-sdk.md)
* [ID Document Recognition iOS SDK](../../../id-document-recognition-sdk/recognition-and-liveness/mobile-sdk/id-document-recognition-ios-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
