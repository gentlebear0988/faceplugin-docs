---
description: >-
  Faceplugin on-premise Face Recognition, Liveness, and ID Document Recognition
  SDK docs. Offline Android, iOS, Flutter, React Native, Windows, and Linux
  Docker integration guides.
icon: hand-wave
layout:
  width: default
  title:
    visible: true
  description:
    visible: false
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
  anchors:
    visible: true
---

# Faceplugin SDK Documentation

### Introduction

Faceplugin provides **offline biometric SDKs** for **face recognition**, **face liveness detection (anti-spoofing)** and **ID document recognition**. Every SDK runs entirely on the user's device or on your own **Windows, Linux or Docker** server, so biometric data never leaves your infrastructure and there are no per-call cloud fees.

This documentation shows you how to choose the right SDK, install it on your platform, license it and integrate it into your app through native code or the HTTP API.

### Why Choose an On-Premise Biometric SDK?

* **Privacy by design:** face images, templates and document data stay on your hardware.
* **No per-transaction cloud costs:** processing runs locally, not on a metered cloud API.
* **Works offline:** recognition and verification keep running without an internet connection.
* **Full data ownership:** store face templates and document results in your own database.

### What you can build

* **Passport OCR & ID verification** — classify and read **16,900** document templates from **255** countries (MRZ — the machine-readable zone on passports — plus barcode and optional authenticity). Start: [ID Document capabilities](products/id-document-recognition-sdk/capabilities.md) · [Passport OCR guide](help/resources/passport-ocr-and-id-verification.md)
* **Face recognition & liveness** — offline 1:1 match, mobile 1:N Identify, standalone face anti-spoofing, or recognition + liveness in one server process. Start: [Face SDK capabilities](products/face-recognition-sdk/capabilities.md)

Product hubs: [Face SDK](products/face-recognition-sdk/) · [ID Document SDK](products/id-document-recognition-sdk/)&#x20;

This documentation is the **integration** layer: clone a public GitHub sample, add the Google Drive runtime package or pull Docker Hub, activate a license, then call the APIs that repository actually exposes.

### How to use these docs

1. Read **Capabilities** for the product you need (what it can do).
2. Open **Mobile SDK** or **Server SDK**, then the **platform** page (Android, iOS, Flutter, React Native, Linux, Windows, …).
3. Follow **Setup** / **How to run** to place the Google Drive runtime package and start the **GitHub sample app** for that product.
4. Copy the **machine code** if you run a server SDK, then [request a license](help/request-a-license-and-support.md).
5. Use [Try it](getting-started/try-it.md) and the [HTTP API](integration/http-api/) to call the same server APIs from your app. Store templates and document JSON in **your** database.

Ports: Document **8082**, Face Recognition **8092**, Face Liveness **8084**, Document Liveness **8086**.

For topology and sizing, see [Deploy and host](integration/deploy-and-host/).

Native binaries are **not** on GitHub (too large). Each platform page links the Google Drive folder and the exact copy path.

{% hint style="info" %}
Each GitHub sample app is a complete application for testing. In production, use the runtime directly: copy the runtime, call `activate` → `init`, then call the processing APIs.
{% endhint %}

### Our Products

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="image">Cover image</th><th data-hidden></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Face SDK</strong></td><td>Recognition and liveness on the server, and both together in one mobile SDK</td><td><a href="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/identify.gif">https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/face-recognition/mobile/identify.gif</a></td><td></td><td><a href="products/face-recognition-sdk/">face-recognition-sdk</a></td></tr><tr><td><strong>ID Document SDK</strong></td><td>Passport OCR, authenticity-only liveness, and OCR + authenticity — 16,900 templates</td><td><a href="https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/document-reader/mobile/camera.png">https://raw.githubusercontent.com/Faceplugin-ltd/faceplugin-assets/main/screenshots/document-reader/mobile/camera.png</a></td><td></td><td><a href="products/id-document-recognition-sdk/">id-document-recognition-sdk</a></td></tr></tbody></table>

### Try our SDKs

* [GitHub repos](https://github.com/Faceplugin-ltd)
* [Playground](https://playground.faceplugin.com/)
* [Hugging Face Spaces](https://huggingface.co/Faceplugin-Ltd)
* [Docker Hub](https://hub.docker.com/u/faceplugin)
* [Try it (copy-paste)](getting-started/try-it.md)

### Features <a href="#feature" id="feature"></a>

* Offline after license activation — no Faceplugin cloud inference
* HTTP APIs on ports **8082** (documents), **8092** (face recognition), **8084** (face liveness), **8086** (document liveness)
* Mobile SDKs for Android, iOS, Flutter, React Native, and Ionic (platform support varies by product)

### Platforms <a href="#platform" id="platform"></a>

Platforms vary by product. **Face Recognition** and **ID Document Recognition** include Android, iOS, Flutter, React Native, Ionic, Windows, and Linux/Docker. **Face Liveness** is Android, iOS, Windows, and Linux/Docker (no public Flutter or React Native SDK).

### Common Use Cases

* **eKYC / digital onboarding** — ID OCR + optional authenticity + selfie liveness + face match ([Architecture](integration/deploy-and-host/architecture.md))
* **Banking and fintech** — passport / ID verification and face anti-spoofing on your infrastructure
* **Access control and attendance** — mobile 1:N Identify with templates you store
* **Fraud prevention** — reject presentation attacks and document spoofs before enrollment

### Frequently Asked Questions

**Does Faceplugin's face recognition SDK work offline?**

Yes. All Faceplugin SDKs run on-device or on your own server. No cloud connection is needed to process faces or documents.

**Which countries does the ID document SDK support?**

The ID Document SDK recognizes 16,900 document templates from 255 countries, including passports and national ID cards, and reads MRZ and barcodes.

**Which platforms are supported?**

Face Recognition and ID Document SDKs support Android, iOS, Flutter, React Native, Ionic, Windows and Linux/Docker. The Face Liveness SDK supports Android, iOS, Windows and Linux/Docker.

**How do I get a license for a server SDK?**

Copy the machine code from your server installation and submit it through Request a License & Support.

**Where do I download the SDK binaries?**

Each platform page links to a Google Drive folder with the native binaries. GitHub hosts sample apps only.

### Related documentation

* [Choose a product](getting-started/choose-a-product.md) · [Try it](getting-started/try-it.md) · [HTTP API](integration/http-api/) · [Deploy and host](integration/deploy-and-host/)
* [Glossary](help/resources/glossary.md) · [FAQ](help/resources/faq.md)
* [Request a License & Support](help/request-a-license-and-support.md) · [SDK comparison](help/comparisons/)
* [Status codes](help/resources/status-codes.md) · [Troubleshooting](help/resources/troubleshooting.md) · [Changelog](help/resources/changelog.md)
