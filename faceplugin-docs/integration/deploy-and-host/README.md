---
description: >-
  Deploy and host Faceplugin server SDKs. Architecture, hosting requirements,
  and production checklist for Linux, Docker, and Windows.
icon: server
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

# Deploy and host

Use this section when you put Faceplugin on **your** servers. Faceplugin does not host inference for you after you activate a license.

| Page                                              | Read it when you need to                                     |
| ------------------------------------------------- | ------------------------------------------------------------ |
| [Architecture](architecture.md)                   | Combine products, choose HTTP vs `sdk.py`, know what you own |
| [Hosting requirements](hosting-requirements.md)   | Size CPU, RAM, disk, ports, and Docker flags                 |
| [Production deployment](production-deployment.md) | Harden a first-run install for production                    |

For eKYC servers, prefer [Face Recognition + Liveness](../../products/face-recognition-sdk/recognition-and-liveness/) and [Document Recognition + Liveness](../../products/id-document-recognition-sdk/recognition-and-liveness/) instead of separate recognition-only and liveness-only containers. Start with a platform page for install steps, then use this section for topology and ops.

### Related documentation

* [HTTP API](../http-api/) · [Choose a product](../../getting-started/choose-a-product.md) · [Request a License](../../help/request-a-license-and-support.md)
