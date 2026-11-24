---
description: >-
  Faceplugin ID Document SDK — Recognition. OCR / MRZ / barcode via the Document
  Reader product.
icon: file-lines
---

# Recognition

**OCR / MRZ / barcode / classify / crops** without a dedicated authenticity-only service.

There is no separate “OCR-only” public repository. Document Reader apps expose recognition APIs, and with a Liveness-capable license they also run authenticity — see [Recognition + Liveness](recognition-and-liveness/) for all Document Reader platforms (mobile, Linux, Windows, Node).

* **Capabilities:** [ID Document SDK capabilities](capabilities.md) (Recognition section)
* **Result shape:** [Document result JSON](document-result-json.md)
* **Install platforms:** [Recognition + Liveness](recognition-and-liveness/)

For authenticity **without** OCR, use [Liveness](id-document-liveness-sdk/) on port **8086**.
