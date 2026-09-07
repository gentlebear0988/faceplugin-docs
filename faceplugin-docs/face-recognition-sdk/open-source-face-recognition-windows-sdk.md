---
description: Completely Free and Open Source Face recognition Windows SDK
---

# Open Source Face Recognition Windows SDK

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/Open-Source-Face-Recognition-SDK" %}
Completely Free and Open Source Face recognition Windows SDK
{% endembed %}

### Overview <a href="#setup" id="setup"></a>

The world's 1st **Completely Free** and **Open Source** **Face Recognition SDK** for developers to integrate face recognition capabilities into applications. Supports real-time, high-accuracy face recognition with deep learning models.\
This is **on-premise face recognition SDK** which means everything is processed in your server and **NO** data leaves the machine.\
\
**Please contact us if you need the SDK with higher accuracy.**

### Setup <a href="#setup" id="setup"></a>

Please download anaconda on your computer and install it. We used Windows machine without GPU for testing

1.  **Create anaconda environment**

    `conda create -n facesdk python=3.9`
2.  **Activate env**

    `conda activate facesdk`
3.  **Install dependencies**

    `pip install -r requirements.txt`
4.  **Compare face images in the** `test` **directory**

    `python run.py`

### APIs and Parameters

* **GetImageInfo(image, faceMaxCount):** returns face bounding boxes, landmarks and feature embedding
* **get\_similarity(feat1, feat2):** returns similarity between two feature embeddings. 0 to 100
* **Threshold:** value to determine if two embeddings belong to same person, default = 75
