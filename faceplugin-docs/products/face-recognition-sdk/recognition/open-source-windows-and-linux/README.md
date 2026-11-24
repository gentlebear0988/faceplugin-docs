---
description: >-
  Free on-premise open source Face Recognition SDKs: a Python sample for Windows
  and Linux, and JavaScript, React and Vue SDKs that run in the browser.
icon: code
---

# Open Source

### Code <a href="#code" id="code"></a>

{% embed url="https://github.com/Faceplugin-ltd/Open-Source-Face-Recognition-SDK" %}

### Overview <a href="#overview" id="overview"></a>

The **Open Source Face Recognition SDK** is a free Python sample for Windows and Linux. It detects faces, extracts embeddings, and compares them on your machine (no Faceplugin cloud).

Accuracy is lower than the commercial Face Recognition SDK. Contact Faceplugin if you need the higher-accuracy native engine. This sample is **not** the commercial Android/iOS or HTTP engine.

### The open source family

| SDK | Runs on | Page |
| --- | ------- | ---- |
| **Face Recognition (Python)** | Windows, Linux | [Linux](open-source-face-recognition-linux-sdk.md) · [Windows](open-source-face-recognition-windows-sdk.md) |
| **Face Recognition JavaScript** | The browser, with ONNX Runtime Web and OpenCV.js | [JavaScript](open-source-face-recognition-javascript-sdk.md) |
| **Face Recognition React** | React apps | [React](open-source-face-recognition-react-sdk.md) |
| **Face Recognition Vue** | Vue apps | [Vue](open-source-face-recognition-vue-sdk.md) |
| **Face Liveness Detection (Python)** | Windows, Linux | [Open Source Liveness](../../liveness-detection-sdk/open-source-face-liveness-detection-sdk.md) |

The web SDKs run entirely in the browser, so no image leaves the page. They cover face detection, landmarks, liveness, pose, expression, eye closeness, age, gender and recognition.

### Setup <a href="#setup" id="setup"></a>

#### Prerequisites

* **Python 3.9 or higher**
* **Anaconda** (recommended for dependency management)
* **Windows or Linux** operating system

#### Setup Instructions

1.  **Install Anaconda** (if not already installed)

    ```
    # Download from: https://www.anaconda.com/products/distribution
    ```
2.  **Create and activate conda environment**

    ```
    conda create -n facesdk python=3.9
    conda activate facesdk
    ```
3.  **Install dependencies**

    ```
    pip install -r requirements.txt
    ```
4.  **Test the installation**

    ```
    python run.py
    ```

***

### Quick Start

#### Basic Usage

```
from face_recognition_sdk import FaceRecognition

# Initialize the SDK
face_sdk = FaceRecognition()

# Process an image
image_path = "path/to/your/image.jpg"
face_info = face_sdk.GetImageInfo(image_path, faceMaxCount=10)

# Compare two faces
similarity = face_sdk.get_similarity(feature1, feature2)
```

#### Example: Face Comparison

```
# Compare two images
image1 = "test/1.jpg"
image2 = "test/2.png"

# Get face information from both images
faces1 = face_sdk.GetImageInfo(image1, faceMaxCount=1)
faces2 = face_sdk.GetImageInfo(image2, faceMaxCount=1)

if faces1 and faces2:
    # Compare the first face from each image
    similarity = face_sdk.get_similarity(faces1[0]['embedding'], faces2[0]['embedding'])
    print(f"Similarity: {similarity}%")
    
    # Check if it's the same person (threshold = 75)
    is_same_person = similarity >= 75
    print(f"Same person: {is_same_person}")
```

***

### APIs

### Core Functions

**`GetImageInfo(image_path, faceMaxCount)`**

Extracts face information from an image.

**Parameters:**

* `image_path` (str): Path to the input image
* `faceMaxCount` (int): Maximum number of faces to detect

**Returns:**

* List of dictionaries containing:
  * `bbox`: Face bounding box coordinates
  * `landmarks`: Facial landmark points
  * `embedding`: Feature embedding vector

**`get_similarity(feature1, feature2)`**

Compares two face feature embeddings.

**Parameters:**

* `feature1` (array): First face embedding
* `feature2` (array): Second face embedding

**Returns:**

* Similarity score (0-100), where higher values indicate greater similarity

#### Configuration

* **Default Threshold**: 75 (for determining if two faces belong to the same person)
* **Supported Formats**: JPG, PNG, BMP, TIFF
* **Face Detection**: Automatic detection of multiple faces per image<br>

### Frequently Asked Questions

**Is this the commercial SDK?**&#x20;

No. Contact Faceplugin for the higher-accuracy native engine.

**Does it run on-premise?**&#x20;

Yes. Processing stays on the user’s Windows or Linux machine.

### Related documentation

* [Faceplugin Face Recognition Server SDK](../server-sdk/) · [Mobile SDK](../../recognition-and-liveness/mobile-sdk/) · [Glossary](../../../../help/resources/glossary.md)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
