---
description: Face recognition iOS SDK with liveness detection
---

# Face Recognition iOS SDK

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-iOS" %}

### Setup <a href="#setup" id="setup"></a>

1. Copy `facesdk.framework`directory to the `root` folder of your project.
2. Add `SDK framework` to the project in `Xcode`
3. Add `FaceDemo-Bridging-Header.h` to Build Settings

### APIs

#### <mark style="color:orange;">setActivation:</mark> This API is used to activate the SDK <a href="#setactivation" id="setactivation"></a>

```objectivec
+(int)setActivation:(NSString*)license;
```

| **Input**        | <ul><li><strong>license</strong> (NSString*): The license string</li></ul>                             |
| ---------------- | ------------------------------------------------------------------------------------------------------ |
| **Return value** | <p>The SDK activation status code.</p><ul><li>0: Success</li><li>Non-zero: Activation failed</li></ul> |

#### <mark style="color:orange;">initSDK:</mark> This API is used to initialize the SDK <a href="#initsdk" id="initsdk"></a>

```objectivec
+(int)initSDK;
```

| **Input**        | None                                                                                                                                                                                                                         |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Return value** | <p>The SDK initialization status code.</p><ul><li>0: Success</li><li>-1: License Key Error</li><li>-2: License AppID Error</li><li>-3: License Expired</li><li>-4: Activate Error</li><li>-5: Initialize SDK Error</li></ul> |

#### <mark style="color:orange;">faceDetection:</mark> This API is used to detect faces <a href="#facedetection" id="facedetection"></a>

```objectivec
+(NSMutableArray*)faceDetection:(UIImage*)image;
```

| **Input**        | <ul><li><strong>image</strong> (UIImage*): The input image</li></ul> |
| ---------------- | -------------------------------------------------------------------- |
| **Return value** | An array of **FaceBox** objects representing the detected faces.     |

#### <mark style="color:orange;">templateExtraction:</mark> This API is used to extract face template <a href="#templateextraction" id="templateextraction"></a>

```objectivec
+(NSData*)templateExtraction:(UIImage*)image faceBox:(FaceBox*)faceBox;
```

| **Input**        | <ul><li><strong>image</strong> (UIImage*): The input image</li><li><strong>faceBox</strong> (<strong>FaceBox*</strong>): The bounding box of the detected face</li></ul> |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Return value** | A NSData representing the extracted template from the face                                                                                                               |

#### <mark style="color:orange;">similarityCalculation:</mark> This API is used to calculate the similarity between two face templates <a href="#similaritycalculation" id="similaritycalculation"></a>

```objectivec
+(float)similarityCalculation:(NSData*)templates1 templates2:(NSData*)templates2;
```

| **Input**        | <ul><li><strong>templates1</strong> (NSData*): The first face template</li><li><strong>templates2</strong> (NSData*): The second face template</li></ul> |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Return value** | A float value representing the similarity score between the two face templates The score ranges from 0.0 to 1.0 **Default Threshold is 0.8**             |
