---
description: >-
  Faceplugin SDK glossary. Plain-language definitions for MRZ, OCR, eKYC, 1:1,
  1:N, PAD, FRVT, license key, machine code, template, and HTTP ports used in
  these docs.
icon: book
---

# Glossary

Short definitions for terms used across Faceplugin docs. For product choice, see [Choose a product](../../getting-started/choose-a-product.md). For common questions, see [FAQ](faq.md).

## Products and flows

| Term                                          | Meaning                                                                                                                            |
| --------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| **Face Recognition**                          | Detect faces, extract a face **template**, and compare faces (1:1 or mobile 1:N).                                                  |
| **Face Liveness**                             | Face **anti-spoofing** — is the camera seeing a live person, or a photo/screen/mask?                                               |
| **ID Document Recognition** (Document Reader) | Classify and read ID documents (passport OCR, MRZ, barcode, optional authenticity).                                                |
| **ID Document Liveness**                      | Document **authenticity only** (no OCR) — is the ID image a real card or a spoof?                                                  |
| **eKYC**                                      | Electronic know-your-customer / digital identity onboarding. Usually: scan ID → check authenticity → selfie liveness → face match. |
| **On-premise / offline**                      | After license activation, inference runs on **your** device or server — not a Faceplugin cloud API.                                |

## Matching and anti-spoofing

| Term                         | Meaning                                                                                                                                                                                                             |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1:1**                      | Compare two faces (or two templates) — “is this the same person?”                                                                                                                                                   |
| **1:N Identify**             | Compare one face against **many** enrolled templates. On mobile, Face Recognition demos do this with VideoWorker. The server HTTP API has **no** built-in gallery — you store templates and call `/api/similarity`. |
| **Template / embedding**     | Numeric face feature vector from `templateExtraction` / `POST /api/feature`. You store it in **your** database.                                                                                                     |
| **Similarity / match score** | How alike two templates (or images) are. Mobile Identify default match threshold **0.67**.                                                                                                                          |
| **Passive liveness**         | Anti-spoofing **without** a smile / turn-head challenge. Score a frame or JPEG.                                                                                                                                     |
| **Active liveness**          | User must follow a challenge (smile, turn head). Faceplugin’s Face Liveness product is **passive**. Separate active demos exist on GitHub but are not the Face Liveness SDK.                                        |
| **PAD**                      | Presentation-attack detection — another name for face (or document) anti-spoofing.                                                                                                                                  |
| **Liveness score**           | Face Liveness: score **≥ 0.5** → Real / pass (default in these docs).                                                                                                                                               |

## Documents

| Term                      | Meaning                                                                                                                                                                   |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **OCR**                   | Optical character recognition — reading printed text from the ID image.                                                                                                   |
| **MRZ**                   | Machine-readable zone — the two or three lines of characters at the bottom of many passports and some IDs.                                                                |
| **Document template**     | A known ID layout the engine can classify (Faceplugin catalogs **16,900** templates / **255** countries).                                                                 |
| **Document authenticity** | Checks that the ID image is a physical document (not a screen reprint, etc.). Needs a Liveness-capable license on Document Recognition, or the Document Liveness product. |

## Licensing and servers

| Term                     | Meaning                                                                                                                                                                          |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **License key**          | The activation string Faceplugin issues for your app id (mobile) or machine code (server). Paste it into `license.txt` or send it to `POST /api/activate`.                       |
| **Machine code**         | Server fingerprint from `GET /api/machinecode` (or the API log). Send it when you request a server license. Docker and a native host on the same PC produce **different** codes. |
| **Google Drive runtime** | Large native binaries (AAR, frameworks, `.so` / `.dll`, `.fpk`) linked from each platform page — not stored on GitHub.                                                           |
| **Port 8082**            | ID Document Recognition HTTP API                                                                                                                                                 |
| **Port 8092**            | Face Recognition HTTP API (detect, quality, template, 1:1 match, 1:N identify)                                                                              |
| **Port 8084**            | Face Liveness HTTP API                                                                                                                                                           |
| **Port 8086**            | ID Document Liveness HTTP API                                                                                                                                                    |

## Standards (as used in these docs)

| Term          | Meaning                                                                                                                                                                    |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **NIST FRVT** | Face Recognition Vendor Test — Faceplugin Face Recognition is described as **evaluated** on FRVT.                                                                          |
| **iBeta**     | Independent biometric testing lab often cited for liveness. These docs **do not** claim iBeta certification; ask Faceplugin for a certification statement if you need one. |

### Related documentation

* [FAQ](faq.md) · [Choose a product](../../getting-started/choose-a-product.md) · [Try it](../../getting-started/try-it.md)
* [Status codes](status-codes.md) · [Troubleshooting](troubleshooting.md)
