---
description: Face Liveness Detection Linux SDK
---

# Liveness Detection Linux SDK

### Code <a href="#setup" id="setup"></a>

{% embed url="https://github.com/Faceplugin-ltd/FaceLivenessDetection-Linux" %}

### Setup <a href="#setup" id="setup"></a>

1. Build docker image

```
sudo docker build -t face-liveness --progress=plain .
```

2. Run docker image

```
sudo docker run --name faceplugin -d -p 0.0.0.0:8888:8888 face-liveness
```

3. Get machine code

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>Call "get machine code" endpoint to get the machine code</p></figcaption></figure>

4. Contact us to get the license according to the machine code.
5. Activate the sever by using the license obtained from us.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>Call "activate machine" endpoint to activate the SDK</p></figcaption></figure>

6. Liveness detection using raw file

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption><p>Call "check liveness" endpoint to activate the SDK</p></figcaption></figure>

### APIs

#### <mark style="color:orange;">get\_machine\_code:</mark> This API is used to retrieve the code specific to the server on which this SDK is running <a href="#setactivation" id="setactivation"></a>

```python
@app.route('/get-machine-code', methods=['GET'])
def get_machine_code():
    machine_code = getMachineCode()

    response = jsonify({"machineCode": machine_code.decode("utf-8")})

    response.status_code = 200
    response.headers["Content-Type"] = "application/json; charset=utf-8"
    return response
```

| **Input**        | None                                                               |
| ---------------- | ------------------------------------------------------------------ |
| **Return value** | **The string specific to the server on which this SDK is running** |

#### <mark style="color:orange;">activate\_machine:</mark> This API is used to activate the SDK <a href="#initsdk" id="initsdk"></a>

```python
@app.route('/activate-machine', methods=['POST'])
def activate_machine():
    content = request.get_json()
    license = content['license']

    ret = setActivation(license.encode('utf-8'))
    activate_state = ret
    print("activation: ", ret)

    ret = initSDK("data".encode('utf-8'))
    init_state = ret
    print("init: ", ret)

    response = jsonify({"activationStatus": activate_state}, {'initStatus': init_state})

    response.status_code = 200
    response.headers["Content-Type"] = "application/json; charset=utf-8"
    return response
```

#### <mark style="color:orange;">check\_liveness:</mark> This API is used to determine if the faces are real or fake. This also returns if the faces are occuluded, if the mouth is open, if the eyes are closed, if the face image quality is good and if the face is fronted or not. <a href="#facedetection" id="facedetection"></a>

```python
@app.route('/liveness-detection', methods=['POST'])
def check_liveness():
    faces = []
    isNotFront = None
    isOcclusion = None
    isEyeClosure = None
    isMouthOpening = None
    isBoundary = None
    isSmall = None
    quality = None
    luminance = None
    livenessScore = None

    file = request.files['file']

    try:
        image = Image.open(file)
    except:
        result = "Failed to open file"
        faceState = {"is_not_front": isNotFront, "is_occluded": isOcclusion, "eye_closed": isEyeClosure, "mouth_opened": isMouthOpening, 
                        "is_boundary_face": isBoundary, "is_small": isSmall, "quality": quality, "luminance": luminance, "result": result, "liveness_score": livenessScore}
        response = jsonify({"face_state": faceState, "faces": faces})

        response.status_code = 200
        response.headers["Content-Type"] = "application/json; charset=utf-8"
        return response


    image_np = np.asarray(image)

    faceBoxes = (FaceBox * maxFaceCount)()
    faceCount = faceDetection(image_np, image_np.shape[1], image_np.shape[0], faceBoxes, maxFaceCount)

    for i in range(faceCount):
        landmark_68 = []
        for j in range(68):
            landmark_68.append({"x": faceBoxes[i].landmark_68[j * 2], "y": faceBoxes[i].landmark_68[j * 2 + 1]})
        faces.append({"x1": faceBoxes[i].x1, "y1": faceBoxes[i].y1, "x2": faceBoxes[i].x2, "y2": faceBoxes[i].y2, 
                      "liveness": faceBoxes[i].liveness, 
                      "yaw": faceBoxes[i].yaw, "roll": faceBoxes[i].roll, "pitch": faceBoxes[i].pitch,
                      "face_quality": faceBoxes[i].face_quality, "face_luminance": faceBoxes[i].face_luminance, "eye_dist": faceBoxes[i].eye_dist,
                      "left_eye_closed": faceBoxes[i].left_eye_closed, "right_eye_closed": faceBoxes[i].right_eye_closed,
                      "face_occlusion": faceBoxes[i].face_occlusion, "mouth_opened": faceBoxes[i].mouth_opened,
                      "landmark_68": landmark_68})
    
    result = ""
    if faceCount == 0:
        result = "No face"
    elif faceCount > 1:
        result = "Multiple face"
    else:
        livenessScore = faceBoxes[0].liveness
        if livenessScore > livenessThreshold:
            result = "Real"
        else:
            result = "Spoof"
        
        isNotFront = True
        isOcclusion = False
        isEyeClosure = False
        isMouthOpening = False
        isBoundary = False
        isSmall = False
        quality = "Low"
        luminance = "Dark"
        if abs(faceBoxes[0].yaw) < yawThreshold and abs(faceBoxes[0].roll) < rollThreshold and abs(faceBoxes[0].pitch) < pitchThreshold:
            isNotFront = False
        
        if faceBoxes[0].face_occlusion > occlusionThreshold:
            isOcclusion = True

        if faceBoxes[0].left_eye_closed > eyeClosureThreshold or faceBoxes[0].right_eye_closed > eyeClosureThreshold:
            isEyeClosure = True

        if faceBoxes[0].mouth_opened > mouthOpeningThreshold:
            isMouthOpening = True
        
        if (faceBoxes[0].x1 < image_np.shape[1] * borderRate or 
            faceBoxes[0].y1 < image_np.shape[0] * borderRate or 
                faceBoxes[0].x1 > image_np.shape[1] - image_np.shape[1] * borderRate or 
                faceBoxes[0].x1 > image_np.shape[0] - image_np.shape[0] * borderRate):
            isBoundary = True

        if faceBoxes[0].eye_dist < smallFaceThreshold:
            isSmall = True
        
        if faceBoxes[0].face_quality < lowQualityThreshold:
            quality = "Low"
        elif faceBoxes[0].face_quality < hightQualityThreshold:
            quality = "Medium"
        else:
            quality = "High"
        
        if faceBoxes[0].face_luminance < luminanceDarkThreshold:
            luminance = "Dark"
        elif faceBoxes[0].face_luminance < luminanceLightThreshold:
            luminance = "Normal"
        else:
            luminance = "Light"

    faceState = {"is_not_front": isNotFront, "is_occluded": isOcclusion, "eye_closed": isEyeClosure, "mouth_opened": isMouthOpening, 
                    "is_boundary_face": isBoundary, "is_small": isSmall, "quality": quality, "luminance": luminance, "result": result, "liveness_score": livenessScore}
    response = jsonify({"face_state": faceState, "faces": faces})

    response.status_code = 200
    response.headers["Content-Type"] = "application/json; charset=utf-8"
    return response
```
