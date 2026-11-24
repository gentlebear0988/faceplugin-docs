---
description: >-
  Faceplugin Face SDK — Recognition + Liveness server. Combined HTTP API on port
  8083 (Linux Docker, Windows, .NET).
icon: server
---

# Server

Combined **recognition + face liveness** on the server. Port **8083**. Image: `faceplugin/face-recognition-liveness-sdk`.

Same recognition routes as Recognition-only, plus `POST /api/liveness`.

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td><strong>Linux SDK</strong></td><td>Docker image faceplugin/face-recognition-liveness-sdk. Match + /api/liveness on 8083.</td><td><a href="../../../../.gitbook/assets/linux-docker.png">linux-docker.png</a></td><td><a href="face-recognition-sdk-linux.md">face-recognition-sdk-linux.md</a></td></tr><tr><td></td><td><strong>Windows SDK</strong></td><td>One Windows process with detect, match, and /api/liveness.</td><td><a href="../../../../.gitbook/assets/windows.png">windows.png</a></td><td><a href="face-recognition-sdk-windows.md">face-recognition-sdk-windows.md</a></td></tr><tr><td></td><td><strong>.NET SDK</strong></td><td>.NET MAUI and C# bindings with recognition and liveness.</td><td><a href="../../../../.gitbook/assets/dotnet.png">dotnet.png</a></td><td><a href="face-recognition-dot-net-sdk.md">face-recognition-dot-net-sdk.md</a></td></tr></tbody></table>

* [Recognition + Liveness](../) · [Mobile](../mobile-sdk/) · [Capabilities](../../capabilities.md)
* Prefer recognition-only server? [Recognition → Server](../../recognition/server-sdk/)
