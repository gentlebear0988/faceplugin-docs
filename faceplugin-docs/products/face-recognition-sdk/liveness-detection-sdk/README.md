---
description: >-
  Faceplugin Face Liveness Detection SDK and anti-spoofing. On-premise passive
  liveness for Android, iOS, Windows, and Linux Docker. POST /api/liveness on
  port 8084.
icon: user-shield
---

# Liveness

### Overview

**Faceplugin Face Liveness Detection SDK** is an on-premise **face anti-spoofing** / presentation-attack detection (PAD) engine. It answers: is a **live person** in front of the camera, or a printout, phone screen, 3D mask, or deepfake-style video?

**What it can do:** [Face SDK capabilities](../capabilities.md) (Liveness section) — passive anti-spoofing, mobile vs server, and when to use it versus Face Recognition Identify.

This product is **passive** anti-spoofing: no smile / turn-head challenge. These docs do **not** claim iBeta certification. Contact Faceplugin if you need a certification statement. Active challenge demos on GitHub are **not** this product. Optional active prompts on some Face Recognition Identify flows also are not this SDK.

This SDK is **standalone**. It does **not** enroll people or run 1:N. If you already use Face Recognition Identify on mobile, that flow already includes **passive 2D liveness**. Choose this product when you need anti-spoofing **without** face matching.

```mermaid
flowchart LR
  Frame --> PAD
  PAD --> Score
  Score --> RealOrSpoof
```

Capable of detecting:

* **Printed photos**
* **Screen replays**
* **3D models**
* **Deepfakes** (handled as one type of spoof attack — not a separate deepfake product)

**Mobile** apps run a live camera (VideoWorker plus `faceDetection`). **Linux and Windows** expose a Face Liveness **API**: one RGB JPEG over `POST /api/liveness`. Score **0.5 or higher** is Real / pass.

### Platforms

Start with [Face SDK capabilities](../capabilities.md), then **Mobile SDK** or **Server SDK**.

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td><strong>Capabilities</strong></td><td>Face SDK capabilities — Recognition, Liveness, Recognition + Liveness.</td><td><a href="../capabilities.md">capabilities.md</a></td></tr><tr><td></td><td><strong>Mobile SDK</strong></td><td>Android and iOS. Live camera anti-spoofing.</td><td><a href="mobile-sdk/">mobile-sdk</a></td></tr><tr><td></td><td><strong>Server SDK</strong></td><td>Windows and Linux / Docker. JPEG over HTTP on port 8084.</td><td><a href="server-sdk/">server-sdk</a></td></tr></tbody></table>

### Use cases

* **KYC / onboarding selfies** — confirm a live person before face match (`faceDetection` / `POST /api/liveness`)
* **Banking and fintech** — block photo and screen replay attacks on selfie capture
* **Access control** — reject printouts and phone-screen spoofs at entry
* **Exam proctoring** — score camera frames for presentation attacks without a challenge animation
* **Fraud prevention** — JPEG anti-spoofing on your server (port **8084**)

### Frequently Asked Questions

**What is passive liveness detection?**&#x20;

The engine scores a camera frame or JPEG without a user challenge animation. See [Capabilities](https://github.com/gentlebear0988/temp-docs/tree/main/faceplugin-docs/liveness-detection-sdk/capabilities.md).

**What is the difference between this and Face Recognition liveness?**

Face Recognition mobile Identify includes 2D liveness as part of matching. This product is anti-spoofing only.

### Related documentation

* [Face SDK capabilities](../capabilities.md) · [Face SDK](../) · [ID Document SDK](../../id-document-recognition-sdk/)
* [Request a License](../../../help/request-a-license-and-support.md) · [Try it](../../../getting-started/try-it.md) · [FAQ](../../../help/resources/faq.md) · [Glossary](../../../help/resources/glossary.md)
* [Combining products (eKYC)](../../../getting-started/choose-a-product.md) · [SDK comparison](../../../help/comparisons/)
* [Status codes](../../../help/resources/status-codes.md)
