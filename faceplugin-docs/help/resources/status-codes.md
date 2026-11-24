---
description: >-
  Faceplugin SDK status codes for activate and init (0–4), Linux/Windows HTTP
  envelope codes, and document verification / image quality fields. Used across
  Face Recognition, Liveness, and Document SDKs.
icon: hashtag
---

# Status codes

Use this page to decode **activate** and **init** results on mobile, HTTP envelope `code` values on Linux and Windows, and document verification fields in OCR JSON.

### Mobile activate / init

Used by Android / iOS / Flutter / React Native / Ionic plugins:

| Code | Constant                     | Meaning             |
| ---: | ---------------------------- | ------------------- |
|    0 | `SDK_SUCCESS` / `sdkSuccess` | Activate or init OK |
|    1 | `SDK_LICENSE_INVALID`        | Invalid license     |
|    2 | `SDK_LICENSE_EXPIRED`        | Expired             |
|    3 | `SDK_NOT_ACTIVATED`          | Not activated       |
|    4 | `SDK_INIT_FAILED`            | Init failed         |

Process methods typically return JSON strings (or typed objects). Serialize native calls.

### Document result codes (normalized JSON)

| Field                  | 0    | 1    | 2           |
| ---------------------- | ---- | ---- | ----------- |
| Verification           | Pass | Fail | Not checked |
| Image QA `CheckResult` | Fail | Pass | Not checked |

### HTTP envelope (shared routes)

| `code` | Typical meaning                                                 |
| -----: | --------------------------------------------------------------- |
|      0 | OK                                                              |
|     -1 | Empty or invalid license; missing required field on some routes |
|    -10 | Not found                                                       |
|    -19 | Unhandled server error                                          |

Process routes often **skip** the envelope and return engine JSON instead.

### Face Liveness server threshold

Score **≥ 0.5** → Real / pass (product README). Change this default only if Faceplugin support gives you a different recommended threshold for your license.

### Related documentation

* [FAQ](faq.md) · [Glossary](glossary.md) · [Troubleshooting](troubleshooting.md) · [Request a License](../request-a-license-and-support.md)
