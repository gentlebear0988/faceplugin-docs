---
description: Face Liveness Detection iOS SDK
---

# Liveness Detection iOS SDK

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceLivenessDetection-iOS" %}

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

#### <mark style="color:orange;">faceDetection:</mark> This API is used to detect faces and determine if the faces are real or fake <a href="#facedetection" id="facedetection"></a>

```objectivec
+(NSMutableArray*)faceDetection:(UIImage*)image;
```

| **Input**        | <ul><li><strong>image</strong> (UIImage*): The input image</li></ul>                       |
| ---------------- | ------------------------------------------------------------------------------------------ |
| **Return value** | An array of **FaceBox** objects representing the detected faces and their liveness scores. |

\
**FaceBox**

```objectivec
@interface FaceBox : NSObject

@property (nonatomic) int x1;
@property (nonatomic) int y1;
@property (nonatomic) int x2;
@property (nonatomic) int y2;
@property (nonatomic) float liveness;
@property (nonatomic) float yaw;
@property (nonatomic) float roll;
@property (nonatomic) float pitch;
@end
```

The liveness score ranges from 0.0 to 1.0

If it's greater than the threshold, it's real face
