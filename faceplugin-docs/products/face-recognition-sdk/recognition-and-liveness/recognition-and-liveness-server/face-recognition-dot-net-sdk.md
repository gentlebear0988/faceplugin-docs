---
description: >-
  Faceplugin Face Recognition .NET SDK. Fully on-premise face matching and
  liveness for C# and .NET. Activate, Init, DetectFace, Compare.
icon: code
---

# .NET

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

{% hint style="info" %}
This page follows the public [FaceRecognition-.Net](https://github.com/Faceplugin-ltd/FaceRecognition-.Net) API (`GetHardwareId`, `Activate`, `Init`, `DetectFace`, `Compare`). If your DLL exposes different method names, [contact us](../../../../help/request-a-license-and-support.md) with the methods you see.
{% endhint %}

[Request a License & Support](../../../../help/request-a-license-and-support.md) · [Contact us](../../../../help/contact-us.md)

### Frequently Asked Questions

**Which APIs does the .NET face recognition SDK provide?**

`GetHardwareId`, `Activate`, `Init`, `DetectFace` and `Compare`, as published in the public GitHub repository.

**Is this the Windows HTTP API?**

No. This is a native library for C#. For the HTTP API (`POST /api/detect` on port 8083), use  [Face Recognition Windows SDK](../../recognition/server-sdk/face-recognition-windows-sdk.md).

**Does the .NET SDK include liveness detection?**

Yes. `DetectFace` returns face liveness along with pose, eye closedness and face occlusion.

**How do I compare two faces in C#?**

Call `Compare` with the RGB data, width, height and stride of both images. The similarity score is written to the `similarity` array.

**Does it send face data to the cloud?**

No. The SDK is fully on-premise: detection and matching run inside your application.

### Related documentation

* [Faceplugin Face Recognition Server SDK](../../recognition/server-sdk/) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition Windows SDK](../../recognition/server-sdk/face-recognition-windows-sdk.md) (HTTP API)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
