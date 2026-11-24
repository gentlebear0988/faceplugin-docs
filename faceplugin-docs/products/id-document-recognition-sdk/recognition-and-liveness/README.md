---
description: >-
  Faceplugin ID Document SDK — Recognition + Liveness. Document Reader with OCR
  and optional document authenticity (same engine / license flags).
icon: layer-group
---

# Recognition + Liveness

**Document Reader** products: passport / ID OCR **and** document authenticity (document liveness) in one engine when the license allows. Port **8082** on server.

Public READMEs list OCR, MRZ, barcode, quality, crops, and authenticity / security checks. APIs include `recognize` / `documentProcess` and `documentLiveness` (authenticity-only call on the same SDK).

* **Capabilities:** [ID Document SDK capabilities](../capabilities.md)
* **Security fields:** [Document security check fields](../document-security-check-fields.md)
* **Mobile:** [Mobile SDK](mobile-sdk/)
* **Server:** [Server SDK](server-sdk/) — Linux `faceplugin/document-reader`, Windows, Node

Prefer authenticity **without** OCR? Use dedicated [Liveness](../id-document-liveness-sdk/) on port **8086**. For production eKYC, prefer Document Reader (this mode) over splitting OCR and Document Liveness unless you need an authenticity-only service — see [Deploy and host](../../../integration/deploy-and-host/architecture.md).
