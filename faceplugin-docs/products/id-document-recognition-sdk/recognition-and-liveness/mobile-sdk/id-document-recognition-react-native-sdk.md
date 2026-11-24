---
description: >-
  Faceplugin ID Document Recognition React Native SDK. On-premise OCR, MRZ, and
  authenticity. document-reader-sdk plugin, not Expo Go.
icon: react
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

# React Native

Fully on-premise **ID Document Recognition SDK for React Native**. Package: `document-reader-sdk`. **Not Expo Go.** Physical arm64 device.

Android output is **normalized** to the iOS-shaped JSON. Parse it with [Document result JSON](../../document-result-json.md).

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/ID-Document-Recognition-React-Native" %}

### How to Run

{% stepper %}
{% step %}
#### Install tools

Node **18+**, Yarn, React Native environment. Use Yarn as in the repo. **Not Expo Go.**
{% endstep %}

{% step %}
#### Clone and install

```bash
git clone https://github.com/Faceplugin-ltd/ID-Document-Recognition-React-Native.git
cd ID-Document-Recognition-React-Native
yarn
cd example && yarn
```
{% endstep %}

{% step %}
#### Place native runtimes

| Platform | File                    | Example app path             | Your own app path                                  |
| -------- | ----------------------- | ---------------------------- | -------------------------------------------------- |
| Android  | `documentreadersdk.aar` | `example/android/libdocsdk/` | `node_modules/document-reader-sdk/android/libs/`   |
| iOS      | `docsdk.framework`      | `ios/Frameworks/`            | `node_modules/document-reader-sdk/ios/Frameworks/` |

* Android Google Drive: [documentreadersdk.aar](https://drive.google.com/drive/folders/1nDSfvj0WtC1lZgzwFd7471ECVtk-nuYH)
* iOS Google Drive: [docsdk.framework](https://drive.google.com/drive/folders/1do6Ws_BlXGkR_K9jI_ULd1zHjqLGSP4q)
{% endstep %}

{% step %}
#### Run on a phone

```
yarn
cd example
yarn android
```

iOS: `cd example/ios && pod install`, then open the workspace. Wait until Home shows Ready.
{% endstep %}
{% endstepper %}

Keep demo ids: Android `com.faceplugin.documentreader`, iOS `com.faceplugin.documentreader.app`. Android: `minSdkVersion 24` and `abiFilters 'arm64-v8a'`.

### License

Licenses are **offline**. Install from GitHub, not npm:

```bash
yarn add document-reader-sdk@git+https://github.com/Faceplugin-ltd/ID-Document-Recognition-React-Native.git
```

Rebuild the **native** app after install — a JS reload is not enough.

### Integrate into your own app

```ts
import {
  getMachineCode,
  setActivation,
  init,
  recognize,
  locateDocument,
  SDK_SUCCESS,
} from 'document-reader-sdk';

const act = await setActivation('license key');
if (act === SDK_SUCCESS) await init();
const locateJson = await locateDocument(imageUri);
const resultJson = await recognize(frontUri, backUri, 'normal');
```

Permissions: CAMERA + photo library (`READ_MEDIA_IMAGES` / `READ_EXTERNAL_STORAGE` maxSdk 32; `NSCameraUsageDescription` / `NSPhotoLibraryUsageDescription`).

### APIs

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

#### <mark style="color:orange;">setActivation:</mark> This API is used to activate the SDK

```ts
setActivation(license: string): Promise<number>
```

#### <mark style="color:orange;">init:</mark> This API is used to initialize the SDK

```ts
init(): Promise<number>
```

#### <mark style="color:orange;">getMachineCode:</mark> This API is used to retrieve the machine code

```ts
getMachineCode(): Promise<string>
```

#### <mark style="color:orange;">locateDocument:</mark> This API is used to find document corners on a preview frame

```ts
locateDocument(image: ImageInput): Promise<string>
```

**No OCR.** Overlay only.

#### <mark style="color:orange;">recognize:</mark> This API is used to run FullProcess

```ts
recognize(front, back?, authenticity?): Promise<string>
```

Authenticity: `true` / `false` or `'normal'` / `'none'`.

#### <mark style="color:orange;">documentRecognition:</mark> This API is used to run OCR / MRZ / barcode / image quality only

```ts
documentRecognition(front, back?): Promise<string>
```

#### <mark style="color:orange;">documentLiveness:</mark> This API is used to run authenticity / security only

```ts
documentLiveness(front, back?): Promise<string>
```

Also: `getLicenseStatus`, `startNewSession`, `deinit`.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**What is the React Native ID document recognition package name?**

`document-reader-sdk`. Install it from GitHub with Yarn, not from npm.

**Does it work with Expo Go?**

No. The package contains native code and needs a physical arm64 device.

**Is the JSON result the same on Android and iOS?**

Yes. The plugin normalizes Android output to the iOS shape, so one parser works for both.

See [Document result JSON](../../document-result-json.md).

**Why don't my changes show up after installing the package?**

You need to rebuild the native app after installing. A JavaScript reload is not enough.

**Which permissions does the SDK need?**

Camera access, plus photo library access if users pick images from the gallery: `CAMERA` and `READ_MEDIA_IMAGES` on Android, and `NSCameraUsageDescription` and `NSPhotoLibraryUsageDescription` on iOS.

Does the React Native ID document SDK work offline?

Yes. It is fully on-premise: documents are read on the device.

### Related documentation

* [Faceplugin ID Document Recognition Mobile SDK](./) · [Flutter](id-document-recognition-flutter-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition React Native SDK](../../../face-recognition-sdk/recognition-and-liveness/mobile-sdk/face-recognition-react-native-sdk.md)
* [Document result JSON](../../document-result-json.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
