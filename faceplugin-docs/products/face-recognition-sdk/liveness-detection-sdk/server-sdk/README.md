---
description: >-
  Faceplugin Face Liveness API for Windows and Linux Docker. On-premise
  anti-spoofing HTTP POST /api/liveness on port 8093. Score 0.5 or higher is
  Real.
icon: server
---

# Server

Score **one RGB JPEG** over HTTP. `POST /api/liveness` (alias `/api/check_liveness`). Score **0.5 or higher** → Real / pass.

Typical call order: `GET /api/machinecode` → send machine code → `POST /api/activate` → `POST /api/liveness`. Default port **8093**.

{% hint style="info" %}
**The server API is passive only.** It scores one finished image; there is no challenge sequence over HTTP. The guided **active** flow — anti-spoofing gate, then blink / smile / turn your head — is part of the [Mobile SDK](../mobile-sdk/), which covers Android, iOS, Flutter, React Native, .NET MAUI and Ionic.
{% endhint %}

The request takes the image and nothing else: there is **no** algorithm, model or threshold parameter. The runtime fuses its own estimators into one score, and the `pass` verdict uses a fixed cut-off of **0.5** set in native code. Apply your own cut-off to `score` if you need a different one.

Need recognition **and** liveness? Run [Face Recognition Linux](../../recognition/server-sdk/face-recognition-linux-sdk.md) on port **8092** and this product on port **8093**. Do not mix their `lib/cpu` runtimes.

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td><strong>Linux SDK</strong></td><td>Docker image faceplugin/face-liveness. Same HTTP API as Windows.</td><td><a href="../../../../.gitbook/assets/linux-docker.png">linux-docker.png</a></td><td><a href="liveness-detection-linux-sdk.md">liveness-detection-linux-sdk.md</a></td></tr><tr><td></td><td><strong>Windows SDK</strong></td><td>HTTP API on port 8093. Send a JPEG, get a liveness score. No Docker.</td><td><a href="../../../../.gitbook/assets/windows.png">windows.png</a></td><td><a href="liveness-detection-windows-sdk.md">liveness-detection-windows-sdk.md</a></td></tr></tbody></table>

```mermaid
flowchart LR
  JPEG --> PAD
  PAD --> Score
  Score --> RealOrSpoof
```

### Frequently Asked Questions

**What is the Faceplugin face liveness API?**

An HTTP endpoint, `POST /api/liveness`, on port 8093. A score of 0.5 or higher means the face is real (pass).

**What does the liveness API accept?**

One RGB JPEG per request — as JSON `{"image": "<base64>"}` or as a multipart file field `image` (alias `file`). There are no other parameters.

**Does the server API support active liveness?**

No. Active liveness is a mobile feature. The server scores a single image, passively.

**Can I pick a lighter or faster model?**

No. There is no model or algorithm parameter. The runtime decides internally and returns one fused score.

**Can I run face recognition and liveness on the same server?**

Yes. Run Face Recognition on port 8092 and Face Liveness on port 8093 as separate services, without mixing their `lib/cpu` runtimes.

**Does the face liveness API check ID documents?**

No. It checks faces only. For ID document anti-spoofing, see the ID Document Liveness SDK.

**Does the liveness server work offline?**

Yes. After license activation, it runs entirely on your own server.

### Related documentation

* [Capabilities](../../capabilities.md) · [Faceplugin Face Liveness Detection Mobile SDK](../mobile-sdk/) · [Face Liveness Detection SDK](../) · [Glossary](../../../../help/resources/glossary.md)
* [Face Recognition Server SDK](../../recognition/server-sdk/)
* [ID Document Liveness Server SDK](../../../id-document-recognition-sdk/id-document-liveness-sdk/server-sdk/)
* [Request a License](../../../../help/request-a-license-and-support.md) · [Status codes](../../../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../../../getting-started/choose-a-product.md) · [SDK comparison](../../../../help/comparisons/)
