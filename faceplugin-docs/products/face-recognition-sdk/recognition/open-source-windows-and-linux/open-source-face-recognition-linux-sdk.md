---
description: >-
  Faceplugin open-source Face Recognition Linux SDK. Free on-premise Python
  sample. Lower accuracy than the commercial Docker Face Recognition API.
icon: linux
---

# Linux

### Code <a href="#code" id="code"></a>

{% embed url="https://github.com/Faceplugin-ltd/Open-Source-Face-Recognition-SDK" %}
Free open-source Face Recognition Linux SDK
{% endembed %}

### Overview <a href="#overview" id="overview"></a>

Free **open-source** Face Recognition sample for Linux (Python). Detect faces, extract embeddings, and compare them **on your machine** — no Faceplugin cloud.

Accuracy is **lower** than the commercial [Face Recognition Linux SDK](../server-sdk/face-recognition-linux-sdk.md). Contact Faceplugin if you need the higher-accuracy native engine.

### Setup <a href="#setup" id="setup"></a>

Please download anaconda on your computer and install it. We used Linux machine without GPU for testing

1.  **Create anaconda environment**

    `conda create -n facesdk python=3.9`
2.  **Activate env**

    `conda activate facesdk`
3.  **Install dependencies**

    `pip install -r requirements.txt`
4.  **In `face_util/faceutil.py`, confirm the Linux library path points to `libFaceUtil.so`**

    ```
    dll_path = os.path.abspath(os.path.dirname(__file__)) + '/C/libFaceUtil.so'
    ```

    (Windows uses `'/C/FaceUtil.dll'` instead — see the Windows page.)
5.  **Compare face images in the** `test` **directory**

    `python run.py`

### APIs and Parameters

* **GetImageInfo(image, faceMaxCount):** returns face bounding boxes, landmarks and feature embedding
* **get\_similarity(feat1, feat2):** returns similarity between two feature embeddings. 0 to 100
* **Threshold:** value to determine if two embeddings belong to same person, default = 75

### Frequently Asked Questions

**Is Faceplugin's open-source face recognition SDK free?**

Yes. The Linux sample is free and open source on GitHub.

**Does the open-source SDK need a GPU?**

No. It was tested on a Linux machine without a GPU.

**Is it as accurate as the Docker Hub version?**

No. The open-source sample is less accurate. For the commercial face recognition API, use the Face Recognition Linux SDK (`faceplugin/face-recognition`).

**What similarity threshold should I use?**

The default threshold is 75, on a similarity scale of 0 to 100.

**Does it send face data to the cloud?**

No. Detection, embedding extraction and comparison all run locally.

### Related documentation

* [Faceplugin Face Recognition Server SDK](../server-sdk/) · [Open Source Windows](open-source-face-recognition-windows-sdk.md) · [Glossary](../../../../help/resources/glossary.md)
* Lower accuracy than the commercial [Linux HTTP API](../server-sdk/face-recognition-linux-sdk.md).
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
