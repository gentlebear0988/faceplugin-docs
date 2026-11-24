---
description: >-
  Faceplugin Face SDK — Recognition. Server Face Recognition API on port 8092
  with detect, quality, templates, 1:1 verification and 1:N identification.
icon: fingerprint
---

# Recognition

**Face recognition on your own server**: detect, quality, template extraction, 1:1 verification, and 1:N identification against a local gallery. Port **8092**. These products do **not** expose `POST /api/liveness`.

Both server products ship in a **Lite** and a **Plus** edition. They expose the same API, so you can switch between them without changing your integration.

* **Capabilities:** [Face SDK capabilities](../capabilities.md) (Recognition section)
* **Server:** [Server SDK](server-sdk/) — [Docker](server-sdk/face-recognition-linux-sdk.md) (`faceplugin/face-recognition`) · [Windows](server-sdk/face-recognition-windows-sdk.md)
* **Open source:** free Python samples — [Open Source](open-source-windows-and-linux/)

Mobile face recognition apps include **2D liveness on Identify** — they live under [Recognition + Liveness](../recognition-and-liveness/), not here.

For standalone anti-spoofing on the server, see [Liveness](../liveness-detection-sdk/). There is no combined recognition + liveness server product; run the two services side by side instead.
