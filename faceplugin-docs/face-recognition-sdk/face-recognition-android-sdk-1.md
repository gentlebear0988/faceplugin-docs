---
description: >-
  Explore face recognition SDK React Native Plugin from Faceplugin Top-ranked on
  NIST FRVT , coupled with an advanced iBeta level 2 liveness detection engine
  that effectively safeguards against printed
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

# Face Recognition React Native SDK

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-React-Native" %}

### How to Run

#### 1. Set Up React-Native Development Environment

You can refer to React Native [official document](https://reactnative.dev/docs/environment-setup) to setup enviroment.

#### 2. How to run Android App

```
yarn
cd example
yarn android
```

#### 3. How to run the iOS App

```
yarn
cd example
yarn
cd ios
pod install
```

And then, open the `FaceRecognitionReactNative.xcworkspace` in `Xcode` and run.

#### 4. Install license

[This code](https://github.com/Faceplugin-ltd/FaceRecognition-React-Native/blob/0bd448a0b9c5128acd7dfc964afa144d34bd5ccf/example/src/MainPage.tsx#L23-L38) shows how to use the license

```tsx
const initSDKAsync = async () => {
      try {
        if (Platform.OS == 'ios') {
          var ret = await FaceSDKModule.setActivation(
            'ZxU5Xu7XDitkVZ2y6ooEnkM2aianIE0MC8DncGam0djE+QHVQuNClk+9yJkEElg3zj6KSmloIUbcIYzayCmjdqT/qLCpNg4IHx4CuMOSHuafQLDFFSh/C3pU8sfAHn5f57dXKRPS73tSN52hTlQOZtl99J+47U8oShMmcruG6IdWKj//8ZQm3KkiOAxMZ7IOeoj3lRBnWPr4aZeLDTll9jtNqhIW5S1ElUI8R7ViJiJiHgw0FHwqkQAXXcoUly84HuDr4/W7OGNi7yBfOug21TNgCkHc5yaaerRzqEsAFq61YHlAFNmGhNFhscGEqPfROwcU1BLb1HwbrJQCsZJDjQ=='
          );
          console.log('set activation:', ret);
        } else {
          var ret = await FaceSDKModule.setActivation(
            'EGH+RzwjnvhbtLxq49y1nDDSxguXUZPkgifx/9EGeg3qGrrnsZbP4/nQH1hE/BQyt86UqMByIaUuoqUvJORHPnJBOFSkEIEL4GX1FQjMzDZabA/V2HmkEaY9hAXlIkd4aDAdy7YI37hqfGgvsg8OgSi12aM7ScyHwzC/mbFkTSMHMM/+nB9aWBaO79nCSRWQfpOoQjh823fCwVIJc7YPLxJJjlPZaTASx1DPnoZ8qaCl7tVJpmGIwgTlJq/0ywLrok+q6usCkERqVGloab3O4YcfpZ1Pw7kvUjBvGsR5eWaMzS+xx1tlLvcv0F0pRhJeBRln+2Th57AeNMxXFLxfyQ=='
          );
          console.log('set activation:', ret);
        }
```

## API Usage

### 1. FaceSDKModule

#### **Activate the FaceSDKModule**

Call the `setActivation` method:

```javascript
var ret = await FaceSDKModule.setActivation("...");
console.log("set activation:", ret);
```

#### **Initialize the FaceSDKModule**

Call the `initSDK` method:

```javascript
var ret = await FaceSDKModule.initSDK();
```

#### **Set Parameters**

Use the `setParam` method to configure settings:

```javascript
var ret = await FaceSDKModule.setParam(checkLivenessLevel);
```

#### **Extract Faces**

Use the `extractFaces` method to detect and extract faces from an image:

```javascript
var faceBoxes = await FaceSDKModule.extractFaces(uri);
```

#### **Calculate Face Similarity**

Compare two face templates using the `similarityCalculation` method:

```javascript
const similarity = await FaceSDKModule.similarityCalculation(
    face.templates,
    person.templates
);
```

***

### 2. Working with camera stream

#### **Build the Camera Screen & Process Face Detection**

To implement the native camera and process face detection, refer to the `example/src/FaceRecognitionPage.tsx` file in the repository.

```tsx
<FaceRecognitionSdkView style={styles.box} livenessLevel={1} cameraLens={1} />
```

#### **Get Face Detection Results**

Use the following code to listen for face detection events:

```javascript
const eventEmitter = new NativeEventEmitter(FaceSDKModule);
let eventListener = eventEmitter.addListener('onFaceDetected', (event) => {
    setFaces(event);
    if (recognized == false) {
        identifyPerson(event);
    }
});
```

#### **Start & Stop the Camera**

**Start Camera**

```javascript
const startCamera = async () => {
    await FaceSDKModule.startCamera();
}
```

**Stop Camera**

```javascript
const stopCamera = async () => {
    await FaceSDKModule.stopCamera();
}
```
