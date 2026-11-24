---
description: >-
  Faceplugin Face Recognition Ionic Cordova SDK. On-premise face matching and
  liveness. Legacy Cordova plugin. Prefer Capacitor for new Ionic apps.
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

# Ionic Cordova

Fully on-premise **Face Recognition SDK for Ionic Cordova**. Plugin folder: `Faceplugin/`. Plugin id: `face-recognition-cordova`. Prefer [Ionic Capacitor](face-recognition-ionic-capacitor-sdk.md) for new projects.

Demo package name: **`com.faceplugin.facerecognitionsdk`**.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-Ionic-Cordova" %}

### How to run

### ✅ Setup Environment

Node **18+**, npm, Cordova CLI, JDK **17**, Android SDK. iOS: macOS + Xcode 15+, physical iPhone. Put your Apple Team ID in `build.json`.

#### 1. Install Node.js and npm

```bash
node -v
npm -v
```

#### 2. Get the runtimes

* Android AAR: [Google Drive](https://drive.google.com/drive/folders/1kpzYVv9Gbm_pEpDe9-x7FGB4NWZzvez0) → `Faceplugin/src/android/facerecognitionsdk.aar`
* iOS frameworks: [Google Drive](https://drive.google.com/drive/folders/1PKmV-o7gq7s7dDtiNgXPfCi2ZlWaRy5H) → `Faceplugin/src/ios/Frameworks/`

***

### Running the sample on Android

```bash
npm install
ionic cordova plugin add ./Faceplugin
npm run setup:android
npm run android
```

Add camera permission in `platforms/android/app/src/main/AndroidManifest.xml` if it is not already present:

```xml
<uses-permission android:name="android.permission.CAMERA" />
```

### Running the sample on iOS

```bash
npm install
ionic cordova plugin add ./Faceplugin
npm run setup:ios
npm run ios
```

Add `NSCameraUsageDescription` in `Info.plist`. Run on a **physical** iOS device.

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

#### <mark style="color:orange;">faceDetection:</mark> This API is used to detect faces

```ts
faceDetection(image: ImageInput, param?: FaceDetectionParam): Promise<FaceBox[]>
```

#### <mark style="color:orange;">templateExtraction:</mark> This API is used to extract face template

```ts
templateExtraction(image: ImageInput, faceBox: FaceBox): Promise<string>
```

#### <mark style="color:orange;">similarity:</mark> This API is used to calculate similarity between two templates

```ts
similarity(feature1: string, feature2: string): Promise<number>
```

Also: `getLicenseStatus`, `detect`, `extractFeature`, `quality`, VideoWorker (`startVideoWorker`, `ingestLiveCameraFrame`, `exportLastLiveFrame`), `deinit`.

### Integrate into your own app

Prefer [Ionic Capacitor](face-recognition-ionic-capacitor-sdk.md) for new projects. For this Cordova plugin:

1. Place `facerecognitionsdk.aar` in `Faceplugin/src/android/` and the three iOS frameworks in `Faceplugin/src/ios/Frameworks/`.
2. `ionic cordova plugin add ./Faceplugin` then `npm run setup:android` or `setup:ios`.
3. Keep demo id **`com.faceplugin.facerecognitionsdk`** or request a key for **your** id.
4. `ionic serve` cannot load the engine — run on a physical phone.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Is the Cordova plugin a legacy plugin?**

Yes. It is maintained for existing Cordova apps. Use the Capacitor SDK for new Ionic apps.

**Does the Cordova face recognition plugin work offline?**

Yes. After activation with a license key tied to your Cordova app ID, everything runs on the device.

**What is the Cordova plugin ID?**

`face-recognition-cordova`, added from the local `Faceplugin/` folder.

**Can I test it with `ionic serve` or a simulator?**

No. `ionic serve` cannot load the engine, and iOS needs a physical device. Test on a real phone.

**What do I need to build for iOS?**

macOS, Xcode 15 or later, a physical iPhone and your Apple Team ID in `build.json`.

### Related documentation

* [Faceplugin Face Recognition Mobile SDK](./) · [Ionic Capacitor](face-recognition-ionic-capacitor-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [ID Document Recognition Ionic Cordova SDK](../../../id-document-recognition-sdk/recognition-and-liveness/mobile-sdk/id-document-recognition-ionic-cordova-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
