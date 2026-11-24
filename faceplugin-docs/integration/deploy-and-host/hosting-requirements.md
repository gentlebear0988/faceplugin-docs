---
description: >-
  Faceplugin hosting requirements. CPU, RAM, disk, ports, Docker shm-size, and
  OS notes for Face Recognition, Face Liveness, and ID Document server SDKs.
icon: hard-drive
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

# Hosting requirements

Use this page to size a host for Faceplugin **server** SDKs. Numbers match the public App README system requirement tables. Do not invent higher figures than those tables.

All shipping server products are **CPU only**. There is no `lib/gpu/` package in these apps.

**shm** means Docker shared memory (`--shm-size`). Document products need enough shm to unpack model packs such as `dcr.fpk`.

### Ports

| Product                            | API port | Gradio (host only) | Docker image                               | Role                                             |
| ---------------------------------- | -------: | -----------------: | ------------------------------------------ | ------------------------------------------------ |
| ID Document Recognition + Liveness |     8082 |               9002 | `faceplugin/document-reader`               | **Recommended** for OCR + authenticity           || Face Recognition (only)            |     8092 |               9092 | `faceplugin/face-recognition`              | Match only — no `/api/liveness`                  |
| Face Liveness (only)               |     8084 |               9004 | `faceplugin/face-liveness`                 | PAD only — use when you do not need match        |
| ID Document Liveness (only)        |     8086 |               9006 | `faceplugin/document-liveness`             | Authenticity only — use when you do not need OCR |

Gradio is for local demos. Do not expose it in production. See [Production deployment](production-deployment.md).

Prefer the **combined** Face and Document images for eKYC so you do not run separate recognition and liveness containers. Details: [Architecture](architecture.md).

### Per-product sizing

| Product                            | Min CPU                                    | Min RAM | Min disk | Recommended                    | OS notes                                                                                                       | Docker extras                                                                         |
| ---------------------------------- | ------------------------------------------ | ------- | -------- | ------------------------------ | -------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- || Face Recognition (only)            | 2 cores                                    | 4 GB    | 4 GB     | 4 cores / 8 GB RAM / 8 GB disk | Same as combined                                                                                               | `--shm-size=2gb --privileged`. On Linux mount `/etc/machine-id:ro`                    |
| Face Liveness (only)               | 2 cores (Linux) / 4 cores (Windows README) | 4 GB    | 4 GB     | 4 cores / 8 GB RAM / 8 GB disk | Same Docker OS guidance as Face Recognition. Windows 10/11 x64                                                 | `--shm-size=1gb --privileged`. On Linux mount `/etc/machine-id:ro`                    |
| ID Document Recognition + Liveness | 2 cores                                    | 4 GB    | 4 GB     | 4 cores / 8 GB RAM / 8 GB disk | Ubuntu 20.04+ x86\_64; recommend 22.04 / 24.04. Windows 10/11 x64. CPU only                                    | `--shm-size=2gb` is **required**. `--privileged`. On Linux mount `/etc/machine-id:ro` |
| ID Document Liveness (only)        | 2 cores                                    | 4 GB    | 4 GB     | 4 cores / 8 GB RAM / 8 GB disk | Align with Document Reader Linux                                                                               | `--shm-size=2gb` is **required**. `--privileged`. On Linux mount `/etc/machine-id:ro` |

For a typical eKYC box, plan **two** containers: Document Reader (**8082**) + Face Recognition + Liveness (**8092**). Add RAM/CPU for each. Each container keeps its own `lib/cpu/` — do not mix product Drive folders. See [Architecture](architecture.md).

### Docker rules

* On **Docker Desktop** (macOS / Windows), omit the `/etc/machine-id` volume.
* On Linux Engine, mounting `/etc/machine-id` lets several containers on the same host share one machine code for licensing.
* A smaller `--shm-size` than required can crash Document Reader when `dcr.fpk` unpacks.
* Docker Hub images already include the runtime. You do not need Google Drive for Option A Hub pulls.

### Mobile devices

Mobile SDKs run on the phone. Prefer a **physical device** for camera work. Emulator cameras often stay black. See [Troubleshooting](../../help/resources/troubleshooting.md).

This page does not invent phone RAM minimums beyond each platform README.

### Related documentation

* [Architecture](architecture.md) · [Production deployment](production-deployment.md)
