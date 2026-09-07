---
description: Face recognition Android SDK with liveness detection
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

# Face Recognition Android SDK

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-Android" %}

### Setup <a href="#setup" id="setup"></a>

1. Copy the SDK (`libfacesdk` folder) to the `root` folder in your project.
2. Add SDK to the project in `settings.gradle`

```
rootProject.name = "YourProjectName"
include ':app'
include ':libfacesdk'
```

3. Add dependency to your `build.gradle`

```
implementation project(path: ':libfacesdk')
```

### APIs

#### <mark style="color:orange;">setActivation:</mark> This API is used to activate the SDK <a href="#setactivation" id="setactivation"></a>

```java
public static native int setActivation(String var0);
```

| **Input**        | <ul><li><strong>var0</strong> (String): The license string</li></ul>                                   |
| ---------------- | ------------------------------------------------------------------------------------------------------ |
| **Return value** | <p>The SDK activation status code.</p><ul><li>0: Success</li><li>Non-zero: Activation failed</li></ul> |

#### <mark style="color:orange;">init:</mark> This API is used to initialize the SDK <a href="#init" id="init"></a>

```java
public static native int init(AssetManager var0);
```

| **Input**        | <ul><li><strong>var0</strong> (AssetManager): An instance of AssetManager used to access application assets</li></ul>                                                                                                        |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Return value** | <p>The SDK initialization status code.</p><ul><li>0: Success</li><li>-1: License Key Error</li><li>-2: License AppID Error</li><li>-3: License Expired</li><li>-4: Activate Error</li><li>-5: Initialize SDK Error</li></ul> |

#### <mark style="color:orange;">faceDetection:</mark> This API is used to detect faces <a href="#facedetection" id="facedetection"></a>

```java
public static native List<FaceBox> faceDetection(Bitmap var0, FaceDetectionParam var1);
```

| **Input**        | <ul><li><strong>var0</strong> (Bitmap): The Bitmap image</li><li><strong>var1</strong> (<strong>FaceDetectionParam</strong>): Parameters for face detection</li></ul> |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Return value** | A list of **FaceBox** objects representing the detected faces.                                                                                                        |

#### <mark style="color:orange;">templateExtraction:</mark> This API is used to extract face template <a href="#templateextraction" id="templateextraction"></a>

```java
public static native byte[] templateExtraction(Bitmap var0, FaceBox var1);
```

| **Input**        | <ul><li><strong>var0</strong> (Bitmap): The Bitmap image</li><li><strong>var1</strong> (<strong>FaceBox</strong>): The bounding box of the detected face</li></ul> |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Return value** | A byte array representing the extracted template from the face                                                                                                     |

#### <mark style="color:orange;">similarityCalculation:</mark> This API is used to calculate the similarity between two face templates <a href="#similaritycalculation" id="similaritycalculation"></a>

```java
public static native float similarityCalculation(byte[] var0, byte[] var1);
```

| **Input**        | <ul><li><strong>var0</strong> (byte[]): The byte array representing the first face template</li><li><strong>var1</strong> (byte[]): The byte array representing the second face template</li></ul> |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Return value** | A float value representing the similarity score between the two face templates The score ranges from 0.0 to 1.0 **Default Threshold is 0.8**                                                       |
