---
description: >-
  Faceplugin ID Document Recognition Ionic Cordova SDK. Legacy
  DocumentReaderPlugin layout. Prefer Capacitor for new ID scanning apps.
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

Use this guide only if you already have a **Cordova** Ionic app. The native engine matches Capacitor. New projects should use [Ionic Capacitor](id-document-recognition-ionic-capacitor-sdk.md).

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/ID-Document-Recognition-Ionic-Cordova" %}

### How to run

#### ✅ Setup Environment

Install Node.js, Ionic CLI, and Cordova. Physical device recommended (`ionic serve` cannot load the engine).

1. Place `documentreadersdk.aar` at `DocumentReaderPlugin/src/android/documentreadersdk.aar`.
2. Place `docsdk.framework` at `DocumentReaderPlugin/src/ios/Frameworks/docsdk.framework`.
3. Run:

```bash
npm install
npm run setup:android
npm run android
```

Gradle is bootstrapped via `scripts/with-gradle.js`. iOS: put your Apple Team ID in root `build.json`. Physical iPhone; `docsdk` is device arm64.

You can also add the plugin from a local path:

```bash
ionic cordova plugin add ./DocumentReaderPlugin
ionic cordova build android
ionic cordova run android
```

Drive: [Android](https://drive.google.com/drive/folders/1nDSfvj0WtC1lZgzwFd7471ECVtk-nuYH) · [iOS](https://drive.google.com/drive/folders/1do6Ws_BlXGkR_K9jI_ULd1zHjqLGSP4q).

The Android demo license key string in this repo can differ from Capacitor / React Native / Flutter; it is still bound to `com.faceplugin.documentreader`.

### APIs

Same activate → init → locate / recognize / documentRecognition / documentLiveness sequence as the other mobile Document Reader Apps. Status codes **0**–**4**. Native camera helpers: `startLivePreview` / `takeLiveSnapshot` / `stopLivePreview`. Plugin folder: `DocumentReaderPlugin/`.

#### <mark style="color:orange;">setActivation:</mark> This API is used to activate the SDK

```ts
setActivation(license: string): Promise<number>
```

#### <mark style="color:orange;">init:</mark> This API is used to initialize the SDK

```ts
init(): Promise<number>
```

#### <mark style="color:orange;">recognize:</mark> This API is used to run FullProcess

```ts
recognize(front, back?, authenticity?): Promise<string>
```

Also: `getMachineCode`, `locateDocument`, `documentRecognition`, `documentLiveness`, `getLicenseStatus`, `deinit`.

Parse JSON with [Document result JSON](../../document-result-json.md).

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Is the Cordova plugin a legacy plugin?**

Yes. It is for existing Ionic Cordova apps. New apps should use Ionic Capacitor, which runs the same native engine.

**Can I test it with `ionic serve` or a simulator?**

No. `ionic serve` can't load the engine, and iOS needs a physical iPhone because the framework targets device arm64.

**Where do the native runtime files go?**

The Android AAR goes in `DocumentReaderPlugin/src/android/`, and the iOS framework in `DocumentReaderPlugin/src/ios/Frameworks/`.

**What do I need to build for iOS?**

A physical iPhone and your Apple Team ID in the root `build.json`.

**How do I parse the result?**

`recognize` returns JSON. The fields are described in the Document result JSON reference.

### Related documentation

* [Faceplugin ID Document Recognition Mobile SDK](./) · [Ionic Capacitor](id-document-recognition-ionic-capacitor-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition Ionic Cordova SDK](../../../face-recognition-sdk/recognition-and-liveness/mobile-sdk/face-recognition-ionic-cordova-sdk.md)
* [Document result JSON](../../document-result-json.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
