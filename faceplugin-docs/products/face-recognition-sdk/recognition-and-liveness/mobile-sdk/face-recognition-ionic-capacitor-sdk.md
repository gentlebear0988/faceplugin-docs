---
description: >-
  Faceplugin Face Recognition Ionic Capacitor SDK. On-premise face matching and
  liveness. face-recognition-capacitor plugin for new Ionic apps.
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

# Ionic Capacitor

Fully on-premise **Face Recognition SDK for Ionic Capacitor**. Package: `face-recognition-capacitor`. Use this for **new** Ionic apps. Cordova is a [different repo](face-recognition-ionic-cordova-sdk.md).

`ionic serve` / the browser **cannot** load the engine. Open Android Studio or Xcode and run on a **physical** phone.

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Ionic-Capacitor" %}

### How to run

{% stepper %}
{% step %}
#### Install tools

Node **18+**, JDK **17**, Android Studio and/or Xcode 15+. Physical phone with USB debugging.
{% endstep %}

{% step %}
#### Clone, install, build the plugin

```bash
git clone https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Ionic-Capacitor.git
cd FaceRecognition-LivenessDetection-Ionic-Capacitor
npm install
npm run build
cd example && npm install
```
{% endstep %}

{% step %}
#### Place native runtimes

| Platform | File                     | Example app path              | Your own app path                                         |
| -------- | ------------------------ | ----------------------------- | --------------------------------------------------------- |
| Android  | `facerecognitionsdk.aar` | `example/android/libfacesdk/` | `node_modules/face-recognition-capacitor/android/libs/`   |
| iOS      | three frameworks         | `ios/Frameworks/`             | `node_modules/face-recognition-capacitor/ios/Frameworks/` |

* Android GitHub release: [facerecognitionsdk.aar](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-Android/releases/tag/latest)
* iOS GitHub release: [frameworks](https://github.com/Faceplugin-ltd/FaceRecognition-LivenessDetection-iOS/releases/tag/latest) (`facerecognitionsdk`, `FaceRecognitionEngine`, `onnxruntime`)
{% endstep %}

{% step %}
#### Sync and run

```bash
cd example
npm run build
npx cap sync
npx cap open android
```

Or `npx cap open ios`. Wait for the home status bar → **Ready**.
{% endstep %}
{% endstepper %}

Keep demo ids so the included license works:

| Platform              | Id                                  |
| --------------------- | ----------------------------------- |
| Android package name  | `com.faceplugin.facerecognitionsdk` |
| iOS bundle identifier | `com.faceplugin.facerecognitionsdk` |

The demo app has five tabs: Identify, People, Attribute, Liveness and Settings.

### Screenshots

The demo is the same five-tab app on every platform.

| Identify | People | Attribute |
| -------- | ------ | --------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/identify.gif" alt="Faceplugin Face Recognition live 1:N identify with 2D liveness" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/people.gif" alt="Faceplugin Face Recognition enrolling people with the capture coach" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/attribute.gif" alt="Faceplugin Face Recognition face attributes, quality and landmarks" data-size="original"> |

| Liveness | Settings |
| -------- | -------- |
| <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/liveness.gif" alt="Faceplugin passive 2D liveness check on the camera feed" data-size="original"> | <img src="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/settings.png" alt="Faceplugin Face Recognition match, liveness and pose thresholds" data-size="original"> |

### License

Licenses are **offline**. The sample key is only for the demo app. Request a new license key for your own app. [Request a License & Support](../../../../help/request-a-license-and-support.md).

Default thresholds in Settings: identify **0.67**, liveness **0.5**, pose **40°**, eye-close **0.5**.

### Integrate into your own app

Clone the repository next to your app, build the plugin once, and depend on that local copy. There is no npm package to install.

```json
{
  "dependencies": {
    "face-recognition-capacitor": "file:../FaceRecognition-LivenessDetection-Ionic-Capacitor"
  }
}
```

```bash
npm install
npx cap sync
```

Copy the runtimes into the plugin folder at `android/libs/` and `ios/Frameworks/` as in the table above, then run `npx cap sync` again. Set **your** `appId` in `capacitor.config.ts`. Add camera + photo-library permissions.

```ts
import {
  getMachineCode,
  setActivation,
  init,
  lastLicenseError,
  faceDetection,
  templateExtraction,
  cropFace,
  SDK_SUCCESS,
} from 'face-recognition-capacitor';

async function activate() {
  const mc = await getMachineCode(); // machine code — send when requesting a key
  const act = await setActivation('license key');
  if (act !== SDK_SUCCESS) throw new Error(await lastLicenseError());
  const initCode = await init();
  if (initCode !== SDK_SUCCESS) throw new Error(`init failed: ${initCode}`);
}

async function enrollStill(imageUri: string) {
  const faces = await faceDetection(imageUri);
  if (faces.length !== 1) return;
  const template = await templateExtraction(imageUri, faces[0]);
  const cropB64 = await cropFace(imageUri, faces[0]);
  // Store template + crop in YOUR database
}
```

### Worked example

The plugin exposes the raw calls **and** two session helpers that drive the camera for you. Start with the sessions; drop to the raw calls when you need your own pipeline.

```ts
import {
  getMachineCode, setActivation, init, getLicenseStatus,
  faceDetection, templateExtraction, cropFace, similarity,
  IdentifySession, CaptureSession,
  SDK_SUCCESS,
} from 'face-recognition-capacitor';

// 1. Once at start-up.
export async function boot(license: string) {
  const machine = await getMachineCode();      // send this when you request a key
  if (await setActivation(license) !== SDK_SUCCESS) throw new Error('activation failed');
  if (await init() !== SDK_SUCCESS) throw new Error('init failed');
  return getLicenseStatus();
}

// 2. Enrol one person from a photo.
export async function enroll(uri: string, name: string) {
  const faces = await faceDetection(uri);
  if (faces.length !== 1) throw new Error('use a photo with exactly one face');

  const template = await templateExtraction(uri, faces[0]);
  const avatar = await cropFace(uri, faces[0]);
  await store.save({ name, template, avatar });    // your storage
}

// 3. Live 1:N with the session helper.
export async function startIdentify(people: Person[]) {
  const session = new IdentifySession({
    settings: { frontCamera: true, matchThreshold: 0.67, livenessThreshold: 0.5 },
    featureTemplates: people.map(p => p.template),
    onTracking: (boxes, frame) => drawOverlay(boxes, frame, session.overlayMirror),
    onMatch: (personIndex, score) => show(people[personIndex].name, score),
  });

  await session.start();
  return () => session.stop();                 // and session.dispose() when you leave
}

// 4. Guided capture with the oval coach.
const capture = new CaptureSession({ settings: { frontCamera: true } });
await capture.start();
const shot = await capture.captureNow(currentBoxes);   // CaptureResult | null
await capture.stop();

// 5. Or compare two templates yourself, 1:1.
const score = await similarity(templateA, templateB);
```

`IdentifySession` mirrors the overlay for you through `session.overlayMirror`: the native front preview is mirrored on both platforms, the engine frames are not.

### APIs

Call order: `getMachineCode` → `setActivation` → `init` → detect / VideoWorker → `deinit`.

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

`FaceBox` includes geometry (`x1,y1,x2,y2`), pose, liveness / quality / eyes / occlusion, age / gender / emotion / mask / glasses, `landmarks`, `landmarkCount`.

#### <mark style="color:orange;">templateExtraction:</mark> This API is used to extract face template

```ts
templateExtraction(image: ImageInput, faceBox: FaceBox): Promise<string>
```

#### <mark style="color:orange;">similarity:</mark> This API is used to calculate similarity between two templates

```ts
similarity(feature1: string, feature2: string): Promise<number>
```

Also: `getLicenseStatus`, `detect(image, crop?, flags?)`, `extractFeature`, `quality`, `cropFace`, `lastLicenseError`.

#### <mark style="color:orange;">VideoWorker:</mark> This API is used for live 1:N identify

```ts
startVideoWorker(config?: VideoWorkerConfig): Promise<number>
syncVideoWorkerDatabase(featuresB64: string[], matchThreshold?: number): Promise<number>
startLivePreview() / takeLiveSnapshot() / ingestLiveCameraFrame(...)
subscribeVideoWorker(listener)
stopVideoWorker()
```

Optional session helpers in the example: `IdentifySession`, `CaptureSession`.

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**What is the Ionic Capacitor face recognition package name?**

`face-recognition-capacitor`. Capacitor is preferred over Cordova for new Ionic apps.

**Does the Ionic face recognition SDK work offline?**

Yes. Once `setActivation` and `init` succeed with a license key for your app ID, everything runs on the device.

**Can I test it with `ionic serve` or in a browser?**

No. The face engine only loads on a real device. Run the app on a physical phone from Android Studio or Xcode.

**Should I use Capacitor or Cordova?**

Use Capacitor for new Ionic apps. The Cordova version is a separate plugin for existing Cordova projects.

**What is the default match threshold?**

0.67 for Identify and 0.5 for liveness.

### Related documentation

* [Faceplugin Face Recognition Mobile SDK](./) · [Ionic Cordova](face-recognition-ionic-cordova-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* [ID Document Recognition Ionic Capacitor SDK](../../../id-document-recognition-sdk/recognition-and-liveness/mobile-sdk/id-document-recognition-ionic-capacitor-sdk.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
