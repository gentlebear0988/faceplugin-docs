---
description: Face recognition Dot Net SDK with liveness detection
---

# Face Recognition Dot Net SDK

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-.Net" %}

### Setup <a href="#setup" id="setup"></a>

1. Copy`FacepluginSDK.dll` to your project.
2. Include `FaceSDK.cs` to your project.

### APIs

#### <mark style="color:orange;">GetHardwareId:</mark> This API is used to get hardware id of the device on which this SDK is running on <a href="#setactivation" id="setactivation"></a>

```csharp
public String GetHardwareId()
```

#### <mark style="color:orange;">Activate:</mark> This API is used to activate the SDK <a href="#setactivation" id="setactivation"></a>

```csharp
public int Activate(String license)
```

#### <mark style="color:orange;">Init:</mark> This API is used to initialize the SDK call these functions <a href="#setactivation" id="setactivation"></a>

```csharp
public int Init(string modelPath)
```

#### <mark style="color:orange;">DetectFace:</mark> This API is used to detect faces, face liveness and get face attributes including yaw, roll, pitch, eye closedness, face occlusion, call these functions <a href="#similaritycalculation" id="similaritycalculation"></a>

```csharp
public int DetectFace(byte[] rgbData, int width, int height, int stride, [In, Out] ResultBox[] faceBoxes, int faceBoxCount)
```

#### <mark style="color:orange;">Compare:</mark> This API is used to get the similarity of two face images <a href="#similaritycalculation" id="similaritycalculation"></a>

```csharp
public int Compare(byte[] rgbData1, int width1, int height1, int stride1, byte[] rgbData2, int width2, int height2, int stride2, [In, Out] float[] similarity)
```

#### &#x20;<a href="#similaritycalculation" id="similaritycalculation"></a>
