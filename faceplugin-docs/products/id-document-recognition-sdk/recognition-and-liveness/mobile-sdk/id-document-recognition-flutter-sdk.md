---
description: >-
  Faceplugin ID Document Recognition Flutter SDK. On-premise OCR, MRZ, and
  authenticity. document_reader_sdk plugin for Android and iOS.
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

# Flutter

Fully on-premise **ID Document Recognition SDK for Flutter**. Package: `document_reader_sdk`. Android output is **normalized** to the iOS-shaped JSON. Parse it with [Document result JSON](../../document-result-json.md).

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/ID-Document-Recognition-Flutter" %}

### How to Run

{% stepper %}
{% step %}
#### Install tools

Flutter **3.44+** (see the package `pubspec.yaml`). JDK **17**. Run `flutter doctor` and fix reported issues.
{% endstep %}

{% step %}
#### Clone and bootstrap

```
git clone https://github.com/Faceplugin-ltd/ID-Document-Recognition-Flutter.git
cd ID-Document-Recognition-Flutter
dart run tool/bootstrap.dart
```
{% endstep %}

{% step %}
#### Place native runtimes

| Platform | File                    | Example app path             | Your own app path        |
| -------- | ----------------------- | ---------------------------- | ------------------------ |
| Android  | `documentreadersdk.aar` | `example/android/libdocsdk/` | Plugin `android/libs/`   |
| iOS      | `docsdk.framework`      | `ios/Frameworks/`            | Plugin `ios/Frameworks/` |

* Android Google Drive: [documentreadersdk.aar](https://drive.google.com/drive/folders/1nDSfvj0WtC1lZgzwFd7471ECVtk-nuYH)
* iOS Google Drive: [docsdk.framework](https://drive.google.com/drive/folders/1do6Ws_BlXGkR_K9jI_ULd1zHjqLGSP4q)
{% endstep %}

{% step %}
#### Run the example

```
cd example
flutter pub get
flutter run
```

Physical phone. Keep Android `com.faceplugin.documentreader`, iOS `com.faceplugin.documentreader.app`.
{% endstep %}
{% endstepper %}

### License

Licenses are **offline**. Request a new license key for your own app.

### Integrate into your own app

```yaml
dependencies:
  document_reader_sdk:
    git:
      url: https://github.com/Faceplugin-ltd/ID-Document-Recognition-Flutter.git
```

Then `flutter pub get`, copy runtimes, `cd ios && pod install`, and **rebuild** the native app.

```dart
import 'package:document_reader_sdk/document_reader_sdk.dart';

Future<void> boot() async {
  final machine = await getMachineCode(); // machine code
  final act = await setActivation('license key');
  if (act != sdkSuccess) {
    print(await lastLicenseError());
    return;
  }
  final code = await init();
  if (code != sdkSuccess) return;

  final locateJson = await locateDocument(imagePath);
  final resultJson = await recognize(frontPath, backPath, true);
}
```

Authenticity accepts `true` / `false` or `'normal'` / `'none'`. Image input: file path, `file://` URI, content URI, or base64 / `data:` URL.

### APIs

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

#### <mark style="color:orange;">setActivation:</mark> This API is used to activate the SDK

```dart
Future<int> setActivation(String license)
```

#### <mark style="color:orange;">init:</mark> This API is used to initialize the SDK

```dart
Future<int> init()
```

#### <mark style="color:orange;">getMachineCode:</mark> This API is used to retrieve the machine code

```dart
Future<String> getMachineCode()
```

#### <mark style="color:orange;">locateDocument:</mark> This API is used to find document corners on a preview frame

```dart
Future<String> locateDocument(ImageInput image)
```

#### <mark style="color:orange;">recognize:</mark> This API is used to run FullProcess

```dart
Future<String> recognize(ImageInput front, [ImageInput? back, Object authenticity = true])
```

#### <mark style="color:orange;">documentRecognition:</mark> This API is used to run OCR / MRZ / barcode / image quality only

```dart
Future<String> documentRecognition(ImageInput front, [ImageInput? back])
```

#### <mark style="color:orange;">documentLiveness:</mark> This API is used to run authenticity / security only

```dart
Future<String> documentLiveness(ImageInput front, [ImageInput? back])
```

Also: `getLicenseStatus`, `startNewSession`, `recognizeResult` (typed `DocResult`), `deinit`.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**What is the Flutter ID document recognition package name?**

`document_reader_sdk`, added as a git dependency from the ID-Document-Recognition-Flutter repository.

**Can I do passport OCR in Flutter?**

Yes. `recognize` returns OCR, MRZ and barcode data as JSON, and `recognizeResult` gives you the same result as a typed object.

**Does the Flutter ID document SDK work offline?**

Yes. It is on-premise: document reading runs on the device.

**Is the result the same on Android and iOS?**

Yes. Android output is normalized to the iOS-shaped JSON, so one parser works for both.

**How do I turn authenticity checks on or off?**

Pass `true` or `'normal'` to turn them on, and `false` or `'none'` to turn them off. In `recognize`, authenticity is on by default.

### Related documentation

* [Faceplugin ID Document Recognition Mobile SDK](./) · [React Native](id-document-recognition-react-native-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition Flutter SDK](../../../face-recognition-sdk/recognition-and-liveness/mobile-sdk/face-recognition-flutter-sdk.md)
* [Document result JSON](../../document-result-json.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
