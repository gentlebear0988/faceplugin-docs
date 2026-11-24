---
description: >-
  Faceplugin Face Recognition React Native SDK. On-premise 1:1 and 1:N matching
  with 2D liveness. face-recognition-sdk plugin, Yarn 3, not Expo Go.
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

Fully on-premise **Face Recognition SDK for React Native** (Android and iOS). Package: `face-recognition-sdk`. It wraps the same native engines as the Android and iOS Face Recognition SDKs.

All processing stays on the device. **No** biometric data is sent to Faceplugin cloud.

{% hint style="warning" %}
**Expo Go is not supported.** You need a development build (bare React Native or Expo prebuild) because this package includes native Android / iOS code. Use **Yarn 3** as in `package.json` (do not `npm install` at the repo root).
{% endhint %}

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-React-Native" %}

### How to Run

{% stepper %}
{% step %}
#### Install tools

Node **18+**, Yarn, and a React Native environment ([setup guide](https://reactnative.dev/docs/environment-setup)). Physical phone recommended (emulator is limited for camera / liveness).
{% endstep %}

{% step %}
#### Clone and install

```bash
git clone https://github.com/Faceplugin-ltd/FaceRecognition-React-Native.git
cd FaceRecognition-React-Native
yarn
cd example && yarn
```
{% endstep %}

{% step %}
#### Place native runtimes

Native binaries are **not** on GitHub (too large).

| Platform | File                     | Example app path              | Your own app path                                   |
| -------- | ------------------------ | ----------------------------- | --------------------------------------------------- |
| Android  | `facerecognitionsdk.aar` | `example/android/libfacesdk/` | `node_modules/face-recognition-sdk/android/libs/`   |
| iOS      | three frameworks         | `ios/Frameworks/`             | `node_modules/face-recognition-sdk/ios/Frameworks/` |

* Android Google Drive: [facerecognitionsdk.aar](https://drive.google.com/drive/folders/1kpzYVv9Gbm_pEpDe9-x7FGB4NWZzvez0)
* iOS Google Drive: [frameworks](https://drive.google.com/drive/folders/1PKmV-o7gq7s7dDtiNgXPfCi2ZlWaRy5H) (`facerecognitionsdk`, `FaceRecognitionEngine`, `onnxruntime`)
{% endstep %}

{% step %}
#### Run on a phone

```bash
cd example
yarn android
```

On macOS for iOS:

```bash
cd example
cd ios && pod install && cd ..
yarn ios --device
```

Open `example/ios/FaceRecognitionSdkExample.xcworkspace` in Xcode if you prefer. Wait until the home status bar shows **Ready**.
{% endstep %}
{% endstepper %}

Keep the demo package name **`com.faceplugin.facerecognitionsdk`** on Android and iOS so the sample key works.

| Requirement  | Value                                      |
| ------------ | ------------------------------------------ |
| React Native | 0.74.x (example ships 0.74.5)              |
| Android      | minSdk 24, physical device recommended     |
| iOS          | iOS 13+, A12+ recommended, physical device |

| Demo tile     | What it does                                                                        |
| ------------- | ----------------------------------------------------------------------------------- |
| **Enroll**    | Enroll a person from a gallery photo (exactly one face) into the on-device database |
| **Identify**  | Live 1:N camera match (VideoWorker) with 2D liveness                                |
| **Capture**   | Oval coach capture → still with attributes → optional enroll                        |
| **Attribute** | Gallery analysis: landmarks, liveness, pose, quality, age, gender, emotion          |
| **Settings**  | Camera lens, identify / liveness / pose / eye-close thresholds                      |
| **About**     | SDK name and license label                                                          |

### Screenshots

The gallery is **Android** sample UI from `faceplugin-assets`. The React Native plugin wraps the same native engines; there is no separate RN photoshoot in these docs.

| Home                                                                                                                                                                                                                                                                       | Identify                                                                                                                                                                                                   | Capture                                                                                                                                                                                                  |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/android/home.png" alt="Faceplugin Face Recognition Android sample UI (shown on the React Native page; no separate RN screenshot yet)" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/android/identify.png" alt="Faceplugin Face Recognition live identify" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/android/capture.png" alt="Faceplugin Face Recognition oval capture" data-size="original"> |

### License

Licenses are **offline**. The sample key is only for the demo app. Request a new license key for your own app.

Default thresholds in Settings: identify **0.67**, liveness **0.5**, pose **40°**, eye-close **0.5**.

[Request a License & Support](../../../../help/request-a-license-and-support.md).

### Integrate into your own app

```bash
yarn add github:Faceplugin-ltd/FaceRecognition-React-Native
```

1. Copy `facerecognitionsdk.aar` into `node_modules/face-recognition-sdk/android/libs/`
2. Copy the three iOS frameworks into `node_modules/face-recognition-sdk/ios/Frameworks/` and `pod install`
3. Call `setActivation` → `init` before detect / VideoWorker
4. For the optional oval UI (`FaceCapture`), also install peers: `react-native-vision-camera`, `react-native-svg`, plus camera permissions

```ts
import {
  setActivation,
  init,
  faceDetection,
  templateExtraction,
  SDK_SUCCESS,
} from 'face-recognition-sdk';

const code = await setActivation('license key');
if (code === SDK_SUCCESS) {
  await init();
}

const faces = await faceDetection(uri, { allAttributes: true, check_liveness: true });
const probe = await templateExtraction(uri, faces[0]);
const score = await similarity(probe, enrolledTemplateB64);
```

Live frames: pass `ingestLiveCameraFrame(photo, { frontCamera: true })` so Android and iOS share one orientation policy.

Optional Capture UI:

```ts
import { FaceCapture } from 'face-recognition-sdk/capture';

<FaceCapture
  settings={{ camera_lens: 'front', liveness_threshold: 0.5, liveness_level: 0 }}
  onCancel={() => navigation.goBack()}
  onCaptured={(result) => { /* result.uri, result.faceBox, result.cropB64 */ }}
/>
```

Person enrollment data in the example is **local** (`AsyncStorage`). Drive holds the **native runtime only**, not the enrolled gallery.

### APIs

Call order: `getMachineCode` → `setActivation` → `init` → detect / VideoWorker → `deinit`.

One TypeScript API for Android and iOS. Results use the **same FaceBox shape** on both platforms (Android is normalized for you). All methods return **Promises**.

Status codes: **0** Success, **1** Invalid license, **2** Expired, **3** Not activated, **4** Init failed.

```ts
import { setActivation, init, faceDetection, templateExtraction, similarity } from 'face-recognition-sdk';
```

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

#### <mark style="color:orange;">getLicenseStatus:</mark> This API is used to read license status

```ts
getLicenseStatus(): Promise<string>
```

#### <mark style="color:orange;">faceDetection:</mark> This API is used to detect faces

```ts
faceDetection(image: ImageInput, param?: FaceDetectionParam): Promise<FaceBox[]>
```

`FaceBox` includes `x1,y1,x2,y2`, `liveness` / `livenessLabel`, age / gender / emotion, glasses / mask, `attributes`, and `landmarks`. Optional: `normalizeFaceBox` if you parse raw bridge JSON yourself.

#### <mark style="color:orange;">templateExtraction:</mark> This API is used to extract face template

```ts
templateExtraction(image: ImageInput, faceBox: FaceBox): Promise<string>
```

| **Input**        | Image URI / path / base64, and a detected `FaceBox`. |
| ---------------- | ---------------------------------------------------- |
| **Return value** | Template as base64. Store it in **your** database.   |

#### <mark style="color:orange;">similarity:</mark> This API is used to calculate similarity between two templates

```ts
similarity(feature1: string, feature2: string): Promise<number>
```

#### <mark style="color:orange;">detect:</mark> This API is used to detect faces and return engine JSON

```ts
detect(image: ImageInput, crop?: boolean, flags?: number): Promise<string>
```

Also: `extractFeature`, `quality`, `cropFace`, `setLandmarkMode` / `getLandmarkMode` (`14` / `68` / `468`), `lastLicenseError`, `deinit`, `estimatorStatus`.

#### <mark style="color:orange;">VideoWorker:</mark> This API is used for live 1:N identify

```ts
startVideoWorker(config?: VideoWorkerConfig): Promise<number>
syncVideoWorkerDatabase(featuresB64: string[], matchThreshold?: number): Promise<number>
ingestLiveCameraFrame(...): Promise<LiveFrameResult>
subscribeVideoWorker(listener): () => void
stopVideoWorker(): Promise<void>
```

Prefer `ingestLiveCameraFrame` so rotation is handled for you.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**What is the React Native face recognition package name?**

`face-recognition-sdk`, installed with Yarn 3 from the FaceRecognition-React-Native repository.

**Does it work with Expo Go?**

No. The package includes native code, so you need a development build: bare React Native or Expo prebuild.

**Does the React Native face recognition SDK work offline?**

Yes. Detection, matching and liveness all run on the device, with no cloud.

**Which React Native version is supported?**

React Native 0.74.x. The example uses 0.74.5.

**Is there a Face Liveness SDK for React Native?**

There is no public standalone one. Identify includes 2D liveness.

**Are the screenshots React Native?**

No. They show the Android sample UI; the package wraps the native engines.

### Related documentation

* [Faceplugin Face Recognition Mobile SDK](./) · [Flutter](face-recognition-flutter-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [ID Document Recognition React Native SDK](../../../id-document-recognition-sdk/recognition-and-liveness/mobile-sdk/id-document-recognition-react-native-sdk.md)
* There is no public Face Liveness React Native SDK — use Identify 2D liveness or native Android/iOS Liveness.
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
