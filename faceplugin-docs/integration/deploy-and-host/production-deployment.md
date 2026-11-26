---
description: >-
  Production checklist for Faceplugin server SDKs. TLS, health checks, activate,
  no Gradio, networking, scale, and upgrades.
icon: shield-check
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
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

# Production deployment

Use this checklist after a first-run install works. These are **how you run** the product. They are not new product features.

### Before you go live

1. Use a Docker Hub image, or a **fixed image tag** you control. Avoid surprise `latest` changes in production if you need a locked build.
2. Do **not** run Gradio (`demo` / `demo.py`) in production. Gradio is a local test UI only.
3. Activate with `POST /api/activate`. Do not depend on an interactive terminal. Detached Compose has no TTY.
4. Point your load balancer or orchestrator at `GET /api/health`. No license is required for health.
5. Put the APIs behind **your** reverse proxy. End TLS at nginx, Caddy, or your cloud load balancer. Open only the ports you need.
6. Keep the APIs on your private network when you can. The Flask apps allow CORS from any origin (`*`). Do not treat CORS as security.
7. Run **one container or process per product**. Run Face Recognition (**8092**) and, when you need server-side anti-spoofing, Face Liveness (**8093**) and Document Reader with authenticity (**8082**) instead of separate liveness-only services. To scale, add more containers. Follow the license and machine-code rules on each platform page.
8. Read logs from `docker logs` or process stdout. Faceplugin does not collect cloud telemetry from these apps.

### License and machine code

A **machine code** identifies the server or container for licensing.

{% hint style="warning" %}
A Docker container and the Linux host have **different** machine codes. If you run in Docker, request the license with the code from the **container**.
{% endhint %}

On Linux, mounting `/etc/machine-id` into each container can let several containers share one machine code. On Docker Desktop, omit that volume. Each container may need its own license. Details: [Request a License & Support](../../help/request-a-license-and-support.md).

### Upgrade path

1. Pull the new image, or replace files in `lib/cpu/`.
2. Restart the container or process.
3. Activate again if the machine code changed.
4. Smoke-test `GET /api/health` and one process endpoint (for example `/api/detect` or `/api/documentRecognition`).

### What production is not

These surfaces are for evaluation only:

* [Playground](https://playground.faceplugin.com/)
* [Hugging Face Spaces](https://huggingface.co/Faceplugin-Ltd)
* Gradio demos on ports 9002–9006

Do not point production traffic at them.

### Quick links

| Topic                     | Page                                            |
| ------------------------- | ----------------------------------------------- |
| Topology and what you own | [Architecture](architecture.md)                 |
| CPU, RAM, shm, ports      | [Hosting requirements](hosting-requirements.md) |
| Endpoint contracts        | [HTTP API](../http-api/)                        |
| First-run curls           | [Try it](../../getting-started/try-it.md)       |

### Related documentation

* [Deploy and host](./) · [Troubleshooting](../../help/resources/troubleshooting.md) · [Status codes](../../help/resources/status-codes.md)
