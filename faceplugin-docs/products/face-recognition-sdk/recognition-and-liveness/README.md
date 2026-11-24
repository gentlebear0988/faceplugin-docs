---
description: >-
  Faceplugin Face SDK — Recognition + Liveness. Mobile Identify with 2D
  liveness, and server combined API on port 8083 with /api/liveness.
icon: layer-group
---

# Recognition + Liveness

Products that ship **face recognition and liveness together**.

### Mobile

On-device enroll / **1:N Identify** with **passive 2D liveness** (same native engine as FaceRecognitionSDK Android / iOS). See [Mobile SDK](mobile-sdk/).

### Server

One process on port **8083**: recognition routes **plus** `POST /api/liveness`. Image: `faceplugin/face-recognition-liveness-sdk`.

See [Server](recognition-and-liveness-server/) for Linux, Windows, and .NET.

**Capabilities:** [Face SDK capabilities](../capabilities.md).

Prefer two separate server products? Run [Recognition](../recognition/) on **8083** and [Liveness](../liveness-detection-sdk/) on **8084**. For production eKYC, prefer this combined Face package — see [Deploy and host](../../../integration/deploy-and-host/architecture.md).
