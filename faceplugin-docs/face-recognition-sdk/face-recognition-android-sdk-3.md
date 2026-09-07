---
description: Face recognition Ionic Cordova SDK with liveness detection
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

# Face Recognition Ionic-Cordova SDK

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceRecognition-Ionic-Cordova" %}

### How to run

### ✅ Setup Environment

Before getting started, ensure the following are installed on your system:

#### 1. Install Node.js and npm

Download the latest version of [Node.js](https://nodejs.org/) which includes npm. After installation, confirm they are set up correctly by running:

```bash
node -v
npm -v
```

#### 2. Install Ionic CLI and Cordova Globally

Install the required tools globally using npm:

```bash
npm install -g @ionic/cli
npm install -g cordova
```

***

### 📱 Running the App on Android

#### Step 1: Integrate FacePlugin

Navigate to your Ionic project directory and add the plugin:

```bash
ionic cordova plugin add ./FacePlugin
```

#### Step 2: Build the Android App

Run the following command to compile your app:

```bash
ionic cordova build android
```

#### Step 3: Add Camera Permission

Locate the `AndroidManifest.xml` file inside:\
`platforms/android/app/src/main/`

Add this permission line within the `<manifest>` tag:

```xml
<uses-permission android:name="android.permission.CAMERA" />
```

#### Step 4: Deploy to Android Device

Once permission is added and the build is successful, run the app on a connected Android device:

```bash
ionic cordova run android
```

***

### 🍏 Running the App on iOS

#### Step 1: Add FacePlugin to Project

From your project root, add the FacePlugin:

```bash
ionic cordova plugin add ./FacePlugin
```

#### Step 2: Add iOS Platform

To enable iOS support, execute:

```bash
ionic cordova platform add ios
```

#### Step 3: Prepare the iOS Project

This prepares the iOS environment and syncs configurations:

```bash
ionic cordova prepare ios
```

#### Step 4: Enable Camera Access in Info.plist

1.  Open the iOS workspace using Xcode:

    ```bash
    open platforms/ios/face-recognition.xcworkspace
    ```
2.  Inside Xcode, locate the `Info.plist` file and add the following snippet to request camera access:

    ```xml
    <key>NSCameraUsageDescription</key>
    <string>We need access to your camera for face recognition.</string>
    ```

#### Step 5: Build and Run

Use Xcode to build and run the app on a physical iOS device (not a simulator).
