# Palm Recognition SDK

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/Palm-Recognition" %}

### Overview <a href="#setup" id="setup"></a>

**World's First Palm Recognition SDK for Any RGB Camera – On-Premise, Real-Time, and High Accuracy**

Introducing the world’s first **Palm Recognition SDK** that works seamlessly with images captured by any standard **RGB camera**. Designed for developers, this powerful SDK enables easy integration of **real-time, high-accuracy palm recognition** into your applications using advanced **deep learning models**.

Unlike cloud-based solutions, our SDK is fully **on-premise**, ensuring that **all data is processed locally on your server** — delivering maximum **data privacy and security**. No internet connection is required, and **no biometric data ever leaves your machine**.

Perfect for secure authentication, access control, and enterprise-grade biometric solutions.

### Setup <a href="#setup" id="setup"></a>

Please download anaconda on your computer and install it. We used Windows machine without GPU for testing

1. Create anaconda environment

```
conda create -n palm python=3.9
```

2. Activate the environment

```
conda activate palm
```

3. Install dependencies

```
pip install torch torchvision torchaudio
pip install opencv-python
pip install tqdm
pip install scikit-image
pip install mediapipe
```

4. Compare two palm images in the `test_images` directory

```
python main.py
```

### APIs and Parameters

* **classify\_hand(mp\_hands, hand\_landmarks, image\_width):** determine if the hand is left hand or right hand
* **extract\_roi(hands, mp\_hands, img\_path):** extract region of interest from the palm image for template matching
* **extract\_features(mp\_hands, hands, path: str):** extract template from the plam image specified by the path parameter
* **compare\_two\_images(mp\_hands, hands, image\_path1, image\_path2, similarity\_threshold=0.8):** compare two hand images to determine if they are the same hand or not.
