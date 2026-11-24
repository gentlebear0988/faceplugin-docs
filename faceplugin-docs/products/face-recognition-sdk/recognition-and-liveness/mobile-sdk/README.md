---
description: >-
  Faceplugin Face Recognition Mobile SDK. On-premise 1:1 and 1:N matching for
  Android, iOS, Flutter, React Native, and Ionic. Offline setActivation, init,
  VideoWorker Identify.
icon: mobile-screen
---

# Mobile

On-device **Face Recognition + Liveness** for Android, iOS, Flutter, React Native, and Ionic. Enroll people, run live **1:N Identify** with **passive 2D liveness**, and store templates in **your** database. Processing stays on the phone.

These apps wrap the same native FaceRecognitionSDK engines (recognition + liveness). They are **not** under Recognition-only. Server combined HTTP API: [Recognition + Liveness](../). Recognition-only server (no `/api/liveness`): [Recognition](../../recognition/) / [Server SDK](../../recognition/server-sdk/).

Typical call order: `setActivation` → `init` → detect / extract template → `similarity` or VideoWorker (live 1:N). Identify default **0.67**. Liveness default **0.5**.

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td><strong>Android SDK</strong></td><td>Native AAR. Enroll, Identify, Capture, and Attribute screens.</td><td><a href="../../../../.gitbook/assets/android.png">android.png</a></td><td><a href="face-recognition-android-sdk.md">face-recognition-android-sdk.md</a></td></tr><tr><td></td><td><strong>iOS SDK</strong></td><td>Xcode frameworks. Same demo screens as Android.</td><td><a href="../../../../.gitbook/assets/apple-logo-3-300x300.png">apple-logo-3-300x300.png</a></td><td><a href="face-recognition-ios-sdk.md">face-recognition-ios-sdk.md</a></td></tr><tr><td></td><td><strong>Flutter SDK</strong></td><td>face_recognition_sdk plugin plus an example app.</td><td><a href="../../../../.gitbook/assets/flutter.png">flutter.png</a></td><td><a href="face-recognition-flutter-sdk.md">face-recognition-flutter-sdk.md</a></td></tr><tr><td></td><td><strong>React Native SDK</strong></td><td>face-recognition-sdk plugin. Yarn 3, not Expo Go.</td><td><a href="../../../../.gitbook/assets/react.png">react.png</a></td><td><a href="face-recognition-react-native-sdk.md">face-recognition-react-native-sdk.md</a></td></tr><tr><td></td><td><strong>Ionic Capacitor SDK</strong></td><td>face-recognition-capacitor for new Ionic apps.</td><td><a href="../../../../.gitbook/assets/ionic.png">ionic.png</a></td><td><a href="face-recognition-ionic-capacitor-sdk.md">face-recognition-ionic-capacitor-sdk.md</a></td></tr><tr><td></td><td><strong>Ionic Cordova SDK</strong></td><td>Legacy Cordova plugin. Prefer Capacitor for new projects.</td><td><a href="../../../../.gitbook/assets/cordova.png">cordova.png</a></td><td><a href="face-recognition-ionic-cordova-sdk.md">face-recognition-ionic-cordova-sdk.md</a></td></tr></tbody></table>

```mermaid
flowchart LR
  Camera --> Detect
  Detect --> Liveness2D
  Liveness2D --> Embedding
  Embedding --> Identify
  Identify --> Result
```

### Related documentation

* [Capabilities](../../capabilities.md) · [Recognition + Liveness](../) · [Face SDK](../../) · [Glossary](../../../../help/resources/glossary.md)
* [Standalone Face Liveness Mobile SDK](../../liveness-detection-sdk/mobile-sdk/)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
