---
description: >-
  Faceplugin Face Liveness Detection iOS SDK. On-premise PAD against photos,
  screens, masks, and deepfakes. Xcode frameworks, setActivation, initSDK,
  detectImage.
icon: apple
---

# iOS

Fully on-premise **Face Liveness SDK for iOS**. Native class: `FaceLivenessSDK`.

| What                                             | Id                                              |
| ------------------------------------------------ | ----------------------------------------------- |
| iOS demo app (Xcode `PRODUCT_BUNDLE_IDENTIFIER`) | `com.faceplugin.faceliveness.app`               |
| Android demo package name                        | `com.faceplugin.faceliveness`                   |
| SDK class / framework                            | `FaceLivenessSDK` in `facelivenessdk.framework` |

Keep the iOS demo bundle **`com.faceplugin.faceliveness.app`** so the included license works. Request a new license key if you change it.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceLivenessDetection-iOS" %}

### Setup <a href="#setup" id="setup"></a>

1. Unzip the frameworks from [Google Drive](https://drive.google.com/drive/folders/1HzREOmFg9kBLbuso1e57j8Hk5Jk41kyr): `facelivenessdk.framework`, `FaceLivenessEngine.framework`, `onnxruntime.framework`.
2. Add the SDK frameworks to the project in Xcode (**Embed & Sign**).
3. Add `FaceLivenessSDK-Bridging-Header.h` to Build Settings.

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

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

#### <mark style="color:orange;">getMachineCode:</mark> This API is used to retrieve the machine code <a href="#getmachinecode" id="getmachinecode"></a>

```objectivec
+(NSString*)getMachineCode;
```

#### <mark style="color:orange;">getLicenseStatus:</mark> This API is used to read license status <a href="#getlicensestatus" id="getlicensestatus"></a>

```objectivec
+(NSString*)getLicenseStatus;
+(BOOL)allowsLiveness;
+(NSString*)lastLicenseError;
```

#### <mark style="color:orange;">detectImage:</mark> This API is used to detect faces and determine if the faces are real or fake <a href="#detectimage" id="detectimage"></a>

```objectivec
+(NSString*)detectImage:(UIImage*)image crop:(BOOL)crop flags:(int)flags;
```

| **Input**        | <ul><li><strong>image</strong> (UIImage*): The input image</li></ul> |
| ---------------- | -------------------------------------------------------------------- |
| **Return value** | JSON string with detected faces and liveness scores.                 |

The liveness score ranges from 0.0 to 1.0. If it's greater than the threshold, it's real face. Demo default threshold is **0.5**.

#### <mark style="color:orange;">VideoWorker:</mark> This API is used for the live camera HUD <a href="#videoworker" id="videoworker"></a>

```objectivec
+(int)startVideoWorkerWithMatchThreshold:(float)threshold;
+(int)addVideoWorkerFrame:(UIImage*)image;
+(int)addVideoWorkerSampleBuffer:(CMSampleBufferRef)sampleBuffer;
+(void)stopVideoWorker;
```

The liveness demo does **not** enroll a match database.

#### <mark style="color:orange;">deinitSDK:</mark> This API is used to unload the engine <a href="#deinitsdk" id="deinitsdk"></a>

```objectivec
+(int)deinitSDK;
```

### Run the demo

1. Place `facelivenessdk`, `FaceLivenessEngine`, and `onnxruntime` frameworks (see Setup).
2. Keep the Xcode bundle id **`com.faceplugin.faceliveness.app`**.
3. Run on a **physical** iPhone. Home tiles: **Liveness**, Settings, About. The demo does **not** enroll a match database.

### Screenshots

Sample demo UI from `faceplugin-assets` (`face-liveness/mobile/`). Not a dedicated iOS photoshoot.

| Home                                                                                                                                                                                                                                                                  | Liveness                                                                                                                                                                                          | Settings                                                                                                                                                                                       | About                                                                                                                                                                                    |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/home.png" alt="Faceplugin Face Liveness sample demo home (mobile UI from faceplugin-assets, not a dedicated iOS photoshoot)" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/liveness.png" alt="Faceplugin Face Liveness live camera" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/settings.png" alt="Faceplugin Face Liveness settings" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-liveness/mobile/about.png" alt="Faceplugin Face Liveness About" data-size="original"> |

Score **≥ 0.5** → real. Call `setActivation` → `initSDK` off the main thread.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Is the iOS SDK passive or active liveness detection?**

Passive. It scores the face in a camera frame or image, with no challenge for the user.

**What liveness threshold should I use on iOS?**

Treat a score of 0.5 or higher as real. This is the demo default.

**Which frameworks do I need?**

`facelivenessdk.framework`, `FaceLivenessEngine.framework` and `onnxruntime.framework`, all added with Embed & Sign.

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
