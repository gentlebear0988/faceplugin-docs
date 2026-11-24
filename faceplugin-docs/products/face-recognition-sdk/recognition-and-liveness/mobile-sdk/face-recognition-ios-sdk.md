---
description: >-
  Faceplugin Face Recognition iOS SDK. Fully on-premise face matching, 1:N
  identify, and 2D liveness. Xcode frameworks, setActivation, initSDK,
  detectImage, VideoWorker.
icon: apple
---

# iOS

Fully on-premise **Face Recognition SDK for iOS**. Detect faces, extract templates, match 1:1 or live 1:N, and check passive 2D liveness on the device.

Native class: `FaceRecognitionSDK` (`facerecognitionsdk.framework`). Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-iOS" %}

### Setup <a href="#setup" id="setup"></a>

1. Unzip the frameworks from [Google Drive](https://drive.google.com/drive/folders/1PKmV-o7gq7s7dDtiNgXPfCi2ZlWaRy5H) next to the Xcode project: `facerecognitionsdk.framework`, `FaceRecognitionEngine.framework`, `onnxruntime.framework`.
2. Add the SDK frameworks to the project in Xcode (**Embed & Sign**).
3. Add `FaceRecognitionSDK-Bridging-Header.h` to Build Settings (`#import <facerecognitionsdk/FaceRecognitionSDK.h>`).
4. Set **your** Signing Team. Demo bundle id: **`com.faceplugin.facerecognitionsdk.app`**. Physical iPhone, iOS 13+. Call **setActivation → initSDK** off the main thread.

### APIs

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
4. Wait until the home status bar shows Ready. Then Enroll / Identify / Capture / Attribute unlock.

Call **setActivation → initSDK** off the main thread. The engine is **not** concurrent.

### Screenshots

The gallery is **Android** sample UI from `faceplugin-assets`. iOS demo tiles match these screens; there is no separate iOS photoshoot in these docs.

| Home                                                                                                                                                                                                                                                               | Identify                                                                                                                                                                                                   | Capture                                                                                                                                                                                                  |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/android/home.png" alt="Faceplugin Face Recognition Android sample UI (shown on the iOS page; no separate iOS screenshot yet)" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/android/identify.png" alt="Faceplugin Face Recognition live identify" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/android/capture.png" alt="Faceplugin Face Recognition oval capture" data-size="original"> |

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
