---
description: >-
  Completely free and open source face liveness detection (anti-spoofing) SDK
  for Windows and Linux. MobileNetV2, Python, CPU only, MIT-0 licensed.
icon: code
---

# Open Source

Free, **MIT-0 licensed** face anti-spoofing for Windows and Linux. It tells a **live face** apart from a **spoofed** one — a printed photo, a replayed video, or a picture on a screen — from a single image, on an ordinary CPU.

This is a lightweight sample, not the commercial engine. Accuracy is lower than the [Face Liveness Detection SDK](./), and there is no PAD evaluation behind it. Contact Faceplugin when you need the commercial engine.

### Code <a href="#code" id="code"></a>

{% embed url="https://github.com/Faceplugin-ltd/Open-Source-Face-Liveness-Detection-SDK" %}

### Overview <a href="#overview" id="overview"></a>

* **100% on-premise** — every image is processed locally, and nothing leaves the machine.
* **Lightweight** — a MobileNetV2 backbone with a small classification head, real time on the CPU.
* **No GPU, no internet** — no CUDA setup, and nothing to download at run time.
* **Free for commercial use** — MIT-0, no attribution required.

What it does today:

| Capability | Detail |
| ---------- | ------ |
| Live vs. fake from one image | Printed photo, video replay and screen attacks |
| Spoof score | `0` (live) to `1` (fake) for every image |
| Batch mode | Every image in a folder |
| Threshold | Adjustable, default **0.10** |
| Formats | JPG, JPEG, PNG, BMP |

{% hint style="info" %}
The whole image is classified — there is no face detection step. Crop to the face first when the frame holds more than a face.
{% endhint %}

### Setup <a href="#setup" id="setup"></a>

Python **3.10 or newer** (64-bit), on Windows or Linux. Anaconda is recommended.

```bash
conda create -n liveness python=3.10
conda activate liveness
pip install -r requirements.txt
python main.py
```

### Quick start

```bash
python main.py                                    # every image in the input folder
python main.py --input photo.jpg                  # one image
python main.py --input my_images --threshold 0.1  # 0.1 is the default
```

| Option | Default | Description |
| ------ | ------- | ----------- |
| `--input` | `input` | An image file, or a folder of images |
| `--weights` | `weights/liveness.hdf5` | Path to the model weights |
| `--threshold` | `0.10` | Spoof scores above this are labeled **Fake** |

Lower the threshold (for example `0.05`) to catch more spoofs, or raise it to reject fewer real faces.

### APIs

```python
from liveness_detector import LivenessDetector

detector = LivenessDetector()                 # load the model once, then reuse it

result = detector.predict_file("input/real.jpg")
print(result.label, result.score)             # e.g. Live 0.0123

result = detector.predict(frame)              # a BGR frame, as from cv2
if result.is_live:
    print("Live face")
```

#### <mark style="color:orange;">LivenessDetector(weights\_path, threshold):</mark> builds the network and loads the weights

| **Input** | **weights\_path** (str): the weights file, default `weights/liveness.hdf5` · **threshold** (float): default `0.10` |
| --------- | ------------------------------------------------------------------------------------------------------------------ |
| **Return value** | A detector you create once and reuse |

#### <mark style="color:orange;">predict\_file(image\_path):</mark> runs liveness detection on an image file

| **Return value** | A `LivenessResult`, or `None` when the image cannot be read |
| ---------------- | ------------------------------------------------------------ |

#### <mark style="color:orange;">predict(image\_bgr):</mark> runs liveness detection on an in-memory image

| **Input** | A BGR NumPy array, as returned by `cv2.imread` or `cv2.VideoCapture` |
| --------- | ---------------------------------------------------------------------- |
| **Return value** | A `LivenessResult` |

#### `LivenessResult`

| Field | Meaning |
| ----- | ------- |
| `score` | Spoof score, `0` (live) to `1` (fake) |
| `is_live` | `True` when the score is at or below the threshold |
| `label` | `"Live"` or `"Fake"` |

Images are resized to 224 × 224 RGB and scaled to the 0–1 range.

### Frequently Asked Questions

**Is this the commercial liveness SDK?**

No. It is a free sample. The commercial [Face Liveness Detection SDK](./) is a different engine, with mobile and server builds and a PAD evaluation behind it.

**Is it really free for commercial use?**

Yes. The license is MIT-0, so no attribution is required.

**Does it need a GPU or an internet connection?**

Neither. It runs on the CPU and works offline.

**Does it detect faces?**

No. It classifies the whole image, so crop to the face when the frame holds more than one.

### Related documentation

* [Face Liveness Detection SDK](./) · [Mobile SDK](mobile-sdk/) · [Server SDK](server-sdk/)
* [Open Source Face Recognition SDK](../recognition/open-source-windows-and-linux/)
* [Glossary](../../../help/resources/glossary.md) · [Request a License](../../../help/request-a-license-and-support.md)
