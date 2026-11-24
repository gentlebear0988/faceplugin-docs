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

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-ReactNative" %}

### How to Run

{% stepper %}
{% step %}
#### Install tools

Node **18+**, Yarn, and a React Native environment ([setup guide](https://reactnative.dev/docs/environment-setup)). Physical phone recommended (emulator is limited for camera / liveness).
{% endstep %}

{% step %}
#### Clone and install

```bash
git clone https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-ReactNative.git
cd FaceRecognition-LivenessDetection-ReactNative
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

* Android GitHub release: [facerecognitionsdk.aar](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Android/releases/tag/latest)
* iOS GitHub release: [frameworks](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-iOS/releases/tag/latest) (`facerecognitionsdk`, `FaceRecognitionEngine`, `onnxruntime`)
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

| Tab | What it does |
| --- | ------------ |
| **Identify** | Live 1:N against everyone enrolled, with passive 2D liveness, then a ranked result screen |
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

Licenses are **offline**. The sample key is only for the demo app. Request a new license key for your own app.

Default thresholds in Settings: identify **0.67**, liveness **0.5**, pose **40°**, eye-close **0.5**.

[Request a License & Support](../../../../help/request-a-license-and-support.md).

### Integrate into your own app

Clone the repository next to your app and depend on that local copy. There is no npm package to install.

```json
{
  "dependencies": {
    "face-recognition-sdk": "file:../FaceRecognition-LivenessDetection-ReactNative"
  }
}
```

1. Copy `facerecognitionsdk.aar` into the plugin folder at `android/libs/`
2. Copy the iOS frameworks into the plugin folder at `ios/Frameworks/`, then run `pod install`
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

### Worked example

A complete enrol-then-identify flow. Store the templates wherever your app already keeps user data — the plugin does not own a gallery.

```ts
import {
  getMachineCode, setActivation, init, getLicenseStatus, lastLicenseError,
  faceDetection, templateExtraction, cropFace, similarity,
  startVideoWorker, syncVideoWorkerDatabase, addVideoWorkerFrame,
  subscribeVideoWorker, stopVideoWorker,
  SDK_SUCCESS,
} from 'face-recognition-sdk';

// 1. Once at start-up, off the render path.
export async function boot(license: string) {
  const machine = await getMachineCode();        // send this when you request a key
  const act = await setActivation(license);
  if (act !== SDK_SUCCESS) throw new Error(await lastLicenseError());
  const code = await init();
  if (code !== SDK_SUCCESS) throw new Error(`init failed: ${code}`);
  return getLicenseStatus();                     // what the key unlocks
}

// 2. Enrol one person from a gallery photo.
export async function enroll(imagePath: string, name: string) {
  const faces = await faceDetection(imagePath);
  if (faces.length !== 1) throw new Error('use a photo with exactly one face');

  const face = faces[0];
  if (face.liveness < 0.5) throw new Error('that photo looks like a spoof');

  const template = await templateExtraction(imagePath, face);
  const avatar = await cropFace(imagePath, face);
  await db.savePerson({ name, template, avatar });   // your storage
}

// 3. Identify on the live camera, 1:N against everyone you stored.
export async function startIdentify(onMatch: (name: string, score: number) => void) {
  const people = await db.allPeople();
  await startVideoWorker({ matchThreshold: 0.67 });
  await syncVideoWorkerDatabase(people.map(p => p.template));

  const off = subscribeVideoWorker(event => {
    if (event.matched) onMatch(people[event.personIndex].name, event.score);
  });

  // feed frames from your camera component
  // await addVideoWorkerFrame(frame)

  return async () => { off(); await stopVideoWorker(); };
}

// 4. Or compare two templates yourself, 1:1.
const score = await similarity(templateA, templateB);
const samePerson = score >= 0.67;
```

Thresholds are arguments, not settings the plugin reads: identify **0.67**, liveness **0.5**, pose **40°**, eye-close **0.5** are the demo's defaults, and you can tune each for your product.

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

`face-recognition-sdk`, installed with Yarn 3 from the FaceRecognition-LivenessDetection-ReactNative repository.

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
