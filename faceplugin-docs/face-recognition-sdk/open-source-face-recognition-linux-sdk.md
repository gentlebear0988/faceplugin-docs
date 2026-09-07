---
description: Completely Free and Open Source Face recognition Linux SDK
---

# Open Source Face Recognition Linux SDK

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/Open-Source-Face-Recognition-SDK" %}
Completely Free and Open Source Face recognition Linux SDK
{% endembed %}

### Overview <a href="#setup" id="setup"></a>

The world's 1st **Completely Free** and **Open Source** **Face Recognition SDK** for developers to integrate face recognition capabilities into applications. Supports real-time, high-accuracy face recognition with deep learning models.\
This is **on-premise face recognition SDK** which means everything is processed in your server and **NO** data leaves the machine.\
\
**Please contact us if you need the SDK with higher accuracy.**

### Setup <a href="#setup" id="setup"></a>

Please download anaconda on your computer and install it. We used Linux machine without GPU for testing

1.  **Create anaconda environment**

    `conda create -n facesdk python=3.9`
2.  **Activate env**

    `conda activate facesdk`
3.  **Install dependencies**

    `pip install -r requirements.txt`
4.  **In faceutil.py in the face\_util directory modify the following code to import `libFaceUtil.so` file in face\_util/c directory**

    `dll_path = os.path.abspath(os.path.dirname(`**`file`**`)) + '/C/face_util.dll'`
5.  **Compare face images in the** `test` **directory**

    `python run.py`

### APIs and Parameters

* **GetImageInfo(image, faceMaxCount):** returns face bounding boxes, landmarks and feature embedding
* **get\_similarity(feat1, feat2):** returns similarity between two feature embeddings. 0 to 100
* **Threshold:** value to determine if two embeddings belong to same person, default = 75
