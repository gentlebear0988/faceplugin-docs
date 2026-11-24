---
description: >-
  Faceplugin ID Document Recognition iOS SDK. On-premise OCR, MRZ, and
  authenticity. docsdk.framework, setActivation, initSDK, locateDocument,
  recognize.
icon: apple
---

# iOS

Fully on-premise **ID Document Recognition SDK for iOS**. Native class: `DocSDK`. Demo bundle: **`com.faceplugin.documentreader.app`**.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/ID-Document-Recognition-iOS" %}

### Setup <a href="#setup" id="setup"></a>

1. Unzip `docsdk.framework.zip` from [Google Drive](https://drive.google.com/drive/folders/1do6Ws_BlXGkR_K9jI_ULd1zHjqLGSP4q) next to `DocumentReader.xcodeproj`. Engine may nest as `Frameworks/dcrcore.framework` inside `docsdk.framework`.
2. Add `docsdk.framework` to the project in Xcode (**Embed & Sign**). Bridging: `#import <docsdk/DocSDK.h>`.
3. Set **your** Signing Team. Physical iPhone, iOS 13+, Xcode 15+.

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

### APIs

#### <mark style="color:orange;">setActivation:</mark> This API is used to activate the SDK <a href="#setactivation" id="setactivation"></a>

```objectivec
+(int)setActivation:(NSString*)license;
```

#### <mark style="color:orange;">initSDK:</mark> This API is used to initialize the SDK <a href="#initsdk" id="initsdk"></a>

```objectivec
+(int)initSDK;
```

#### <mark style="color:orange;">getMachineCode:</mark> This API is used to retrieve the machine code <a href="#getmachinecode" id="getmachinecode"></a>

```objectivec
+(NSString*)getMachineCode;
```

#### <mark style="color:orange;">getLicenseStatus:</mark> This API is used to read recognition and authenticity flags <a href="#getlicensestatus" id="getlicensestatus"></a>

```objectivec
+(NSString*)getLicenseStatus;
+(NSString*)lastLicenseError;
```

#### <mark style="color:orange;">locateDocument:</mark> This API is used to find document corners on a preview frame <a href="#locatedocument" id="locatedocument"></a>

```objectivec
+(NSString*)locateDocument:(UIImage*)image;
```

**No OCR.** Overlay only.

#### <mark style="color:orange;">recognize:</mark> This API is used to run FullProcess <a href="#recognize" id="recognize"></a>

```objectivec
+(NSString*)recognize:(UIImage*)image;
+(NSString*)recognize:(UIImage*)image authenticity:(BOOL)authenticity;
+(NSString*)recognize:(UIImage*)image authenticityMode:(NSString*)mode;
+(NSString*)recognizeFront:(UIImage*)front back:(UIImage*)back authenticityMode:(NSString*)mode;
```

#### <mark style="color:orange;">documentRecognition:</mark> This API is used to run OCR / MRZ / barcode / image quality only <a href="#documentrecognition" id="documentrecognition"></a>

```objectivec
+(NSString*)documentRecognition:(UIImage*)image;
+(NSString*)documentRecognitionFront:(UIImage*)front back:(UIImage*)back;
```

#### <mark style="color:orange;">documentLiveness:</mark> This API is used to run authenticity / security only <a href="#documentliveness" id="documentliveness"></a>

```objectivec
+(NSString*)documentLiveness:(UIImage*)image;
+(NSString*)documentLivenessFront:(UIImage*)front back:(UIImage*)back;
```

#### <mark style="color:orange;">startNewSession:</mark> This API is used to open a FullProcess session before recognize

```objectivec
+(NSString*)startNewSession:(NSString*)optionsJson;
```

#### <mark style="color:orange;">deinitSDK:</mark> This API is used to unload the engine <a href="#deinitsdk" id="deinitsdk"></a>

```objectivec
+(NSString*)deinitSDK;
```

### Run the demo

1. Unzip `docsdk.framework.zip` next to `DocumentReader.xcodeproj`. Engine may nest as `Frameworks/dcrcore.framework` inside `docsdk.framework`.
2. Keep demo bundle **`com.faceplugin.documentreader.app`**.
3. Physical iPhone, iOS 13+, Xcode 15+.
4. Home: Camera, Gallery, About. Result JSON matches [Document result JSON](../../document-result-json.md).

### License

Licenses are **offline**. Request a new license key for your own app.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Can I do passport OCR on iOS with this SDK?**

Yes. `recognize` returns OCR, MRZ, barcode data and cropped images as JSON.

**Does the iOS ID document SDK work offline?**

Yes. After `setActivation` and `initSDK` with a license key for the demo bundle or your own, everything runs on the iPhone.

**What is the difference between recognize, documentRecognition and documentLiveness?**

`recognize` runs everything. `documentRecognition` reads the document without authenticity checks. `documentLiveness` runs authenticity checks without reading the document.

**Which iOS and Xcode versions do I need?**

iOS 13 or later on a physical iPhone, and Xcode 15 or later.

**Which framework do I add to Xcode?**

`docsdk.framework`, added with Embed & Sign. The engine may be nested inside it as `dcrcore.framework`.

### Related documentation

* [Faceplugin ID Document Recognition Mobile SDK](./) · [Android](id-document-recognition-android-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition iOS SDK](../../../face-recognition-sdk/recognition-and-liveness/mobile-sdk/face-recognition-ios-sdk.md)
* [Face Liveness Detection iOS SDK](../../../face-recognition-sdk/liveness-detection-sdk/mobile-sdk/liveness-detection-ios-sdk.md)
* [Document result JSON](../../document-result-json.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
