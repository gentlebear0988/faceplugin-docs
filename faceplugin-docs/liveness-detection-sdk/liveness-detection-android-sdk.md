---
description: Face Liveness Detction Android SDK
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

# Liveness Detection Android SDK

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceLivenessDetection-Android" %}

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

#### <mark style="color:orange;">faceDetection:</mark> This API is used to detect faces and determine if the faces are real or fake <a href="#facedetection" id="facedetection"></a>

```java
public static native List<FaceBox> faceDetection(Bitmap var0, FaceDetectionParam var1);
```

| **Input**        | <ul><li><strong>var0</strong> (Bitmap): The Bitmap image</li><li><strong>var1</strong> (<strong>FaceDetectionParam</strong>): Parameters for face detection</li></ul> |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Return value** | A list of **FaceBox** objects representing the detected faces and their liveness scores.                                                                              |

**FaceDetectionParam**

```java
public class FaceDetectionParam {
    public boolean check_liveness = true; //set it to True to check liveness
    public int check_liveness_level = 0; // 0: accurate model, 1: light model
}
```

**FaceBox**

```java
public class FaceBox {
    public int x1;
    public int y1;
    public int x2;
    public int y2;
    public float liveness;
    public float yaw;
    public float roll;
    public float pitch;
}
```

The liveness score ranges from 0.0 to 1.0

If it's greater than the threshold, it's real face
