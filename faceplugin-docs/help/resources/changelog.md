---
description: >-
  Release history for Faceplugin Face Recognition, Face Liveness, and ID
  Document SDKs. Check each public GitHub repository and Docker Hub for the
  latest tags.
icon: clock-rotate-left
---

# Changelog

Published release notes for Faceplugin SDKs will be listed here.

### Documentation

* **HTTP API hub** — shared and per-product endpoint reference under [HTTP API](../../integration/http-api/)
* **Deploy and host** — [Architecture](../../integration/deploy-and-host/architecture.md), [Hosting requirements](../../integration/deploy-and-host/hosting-requirements.md), [Production deployment](../../integration/deploy-and-host/production-deployment.md)
* **Recognition + Liveness is mobile only** — the combined server package is gone. On a server, run [Face Recognition](../../products/face-recognition-sdk/recognition/server-sdk/) on **8092** and [Face Liveness](../../products/face-recognition-sdk/liveness-detection-sdk/server-sdk/) on **8084** as two services.
* **Lite and Plus editions** — the Face Recognition server ships as `faceplugin/face-recognition:latest` (Lite) and `:latest-plus` (Plus), and as two archives on the Windows release page.
* **Runtimes come from GitHub Releases** — the mobile engines and the server runtime packs are attached to each repository's release instead of a shared drive.
* **.NET MAUI joins the mobile line-up** — see [.NET MAUI](../../products/face-recognition-sdk/recognition-and-liveness/mobile-sdk/face-recognition-dot-net-maui-sdk.md).
* **Open source** — [Face Recognition](../../products/face-recognition-sdk/recognition/open-source-windows-and-linux/) now covers the Python, JavaScript, React and Vue SDKs, and [Face Liveness Detection](../../products/face-recognition-sdk/liveness-detection-sdk/open-source-face-liveness-detection-sdk.md) has a free MIT-0 SDK of its own.

Until dated product release entries appear on this page, use:

* The **Releases** tab on each [public GitHub repository](https://github.com/Faceplugin-ltd)
* Docker Hub tags such as `latest` under [hub.docker.com/u/faceplugin](https://hub.docker.com/u/faceplugin)

### Related documentation

* [Choose a product](../../getting-started/choose-a-product.md) · [HTTP API](../../integration/http-api/) · [Deploy and host](../../integration/deploy-and-host/)
* [Request a License](../request-a-license-and-support.md) · [Glossary](glossary.md)
