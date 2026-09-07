---
description: Face recognition Flutter SDK with liveness detection
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
---

# Face Recognition Flutter SDK

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-Flutter" %}

### How to Run

#### 1. Set Up Flutter Development Environment

You can refer to Flutter [official document](https://docs.flutter.dev/get-started/install) to setup enviroment.

#### 2. How to Run the App

```dart
flutter pub upgrade
flutter run
```

If you are going to run the `iOS` app, please refer to this [documentation](https://docs.flutter.dev/deployment/ios) for detailed instructions.

### APIs

### 1.  Usage of FacesdkPlugin

#### **Activate the FacesdkPlugin**

To activate the **FacesdkPlugin**, use the `setActivation` method with your activation key:

```dart
final _facesdkPlugin = FacesdkPlugin();
...
await _facesdkPlugin
    .setActivation(
        "Os8QQO1k4+7MpzJ00bVHLv3UENK8YEB04ohoJsU29wwW1u4fBzrpF6MYoqxpxXw9m5LGd0fKsuiK"
        "fETuwulmSR/gzdSndn8M/XrEMXnOtUs1W+XmB1SfKlNUkjUApax82KztTASiMsRyJ635xj8C6oE1"
        "gzCe9fN0CT1ysqCQuD3fA66HPZ/Dhpae2GdKIZtZVOK8mXzuWvhnNOPb1lRLg4K1IL95djy0PKTh"
        "BNPKNpI6nfDMnzcbpw0612xwHO3YKKvR7B9iqRbalL0jLblDsmnOqV7u1glLvAfSCL7F5G1grwxL"
        "Yo1VrNPVGDWA/Qj6Z2tPC0ENQaB4u/vXAS0ipg==")
    .then((value) => facepluginState = value ?? -1);  
```

***

#### **Initialize FacesdkPlugin**

Before using the SDK, initialize it with the `init` method:

```dart
await _facesdkPlugin
    .init()
    .then((value) => facepluginState = value ?? -1);
```

***

#### **Set Parameters**

Configure the SDK settings using `setParam`:

```dart
await _facesdkPlugin
    .setParam({'check_liveness_level': livenessLevel ?? 0});
```

***

#### **Extract Face Features**

Use the `extractFaces` method to detect and extract face features from an image:

```dart
final faces = await _facesdkPlugin.extractFaces(image.path);
```

***

#### **Calculate Face Similarity**

Compare two face templates and calculate their similarity score:

```dart
double similarity = await _facesdkPlugin.similarityCalculation(
    face['templates'], person.templates) ?? -1;
```

***

### 2. Working with camera stream

#### **Native Camera Integration & Face Detection**

To integrate the **native camera** and implement **real-time face detection and processing**, refer to [this file](https://github.com/Faceplugin-ltd/FaceRecognition-Flutter/blob/main/lib/facedetectionview.dart) in the GitHub repository:

