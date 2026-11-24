---
description: >-
  ID Document Recognition result JSON. documentName, OCR, MRZ, barcode,
  verification, image quality, images, and security fields returned by recognize
  and documentProcess on every platform.
icon: brackets-curly
---

# ID Document Recognition JSON Response

`recognize` (mobile) and `POST /api/documentProcess` (Linux / Windows) return the **same idea**: one JSON object. Dedicated `documentRecognition` / `documentLiveness` (and the matching HTTP paths) use the same JSON shape with recognition-only or authenticity-only fields populated. Flutter, React Native, and Ionic **normalize Android output** so it matches this **iOS-shaped** contract.

Parse this object in your app. Do not copy the demo Result screen.

### Top-level fields

| Field          | Meaning                                                                                    |
| -------------- | ------------------------------------------------------------------------------------------ |
| `errorCode`    | Optional engine error                                                                      |
| `documentName` | Document type name                                                                         |
| `countryName`  | Issuing country                                                                            |
| `score`        | Document / locate confidence                                                               |
| `msg`          | Optional message                                                                           |
| `verification` | Field and document checks                                                                  |
| `imageQuality` | Capture quality checks (omitted when `response.ImageQuality` is `"none"`)                  |
| `ocr`          | Visual-zone fields                                                                         |
| `mrz`          | Machine-readable zone                                                                      |
| `barcode`      | Barcode / QR fields                                                                        |
| `images`       | Crops (portrait, document, signature, …). Recognition only; omitted on `documentLiveness`. |
| `security`     | Authenticity / document liveness (license-gated)                                           |

### `verification`

| Key        | Meaning                       |
| ---------- | ----------------------------- |
| `overall`  | Whole-document verdict        |
| `docType`  | Type check                    |
| `expiry`   | Expiry check                  |
| `text`     | OCR consistency               |
| `mrz`      | MRZ check                     |
| `security` | Authenticity rolled up        |
| `imageQA`  | Image quality rolled up       |
| `portrait` | Portrait check                |
| `reasons`  | Optional list of fail reasons |

Values: **0** Pass, **1** Fail, **2** Not checked.

### `imageQuality.checks`

Typical keys (order used in the Android demo): `focus`, `glares`, `resolution`, `colorness`, `perspective`, `bounds`, `portrait`, `handwritten`, `brightness`, `occlusion`.

Each check uses `CheckResult`: **0** Fail, **1** Pass, **2** Not checked. That is **not** the same 0/1/2 mapping as `verification`.

### `images[]`

Each item: `name`, `image` (often base64), optional `source`. Present on `documentRecognition` / `recognize` / `documentProcess`. **Omitted** on `documentLiveness`.

### `security`

Authenticity / document liveness (license-gated). Full key list: [Document security check fields](document-security-check-fields.md).

| Key       | Meaning                                                                                                                   |
| --------- | ------------------------------------------------------------------------------------------------------------------------- |
| `overall` | Rolled-up authenticity (`success` / `fail` / `notChecked`)                                                                |
| `label`   | Human-readable status                                                                                                     |
| `pages[]` | Per-page groups (`pageIndex`, `overall`, `label`, plus RGB check keys such as `photoOriginAnalysis`, `hologramIntegrity`) |

Each group is `{ title, result, checks? }` or `{ title, score }` for pattern percent. Nested checks are omitted when they were not run.

Needs authenticity `"normal"` on `recognize` / `"Authenticity": "normal"` on the server. `"none"` skips Security.

### Locate JSON (camera overlay only)

`locateDocument` is **not** folded into the recognize result. Expect `score`, `position.corners`, and sometimes `_locateImageWidth` / `_locateImageHeight`. **No OCR** and **no image quality** on this path (`ImageQuality` is `"none"`).

Flutter, React Native, and Ionic ship typed helpers (`recognizeResult`, `rows`, `summary`, `images`, `securityRows`) so you do not write a 600-line parser.

[Request a License & Support](../../help/request-a-license-and-support.md) · [Contact us](../../help/contact-us.md)

### Frequently Asked Questions

**Is the document result JSON the same on every platform?**

Yes. `recognize` on mobile and `POST /api/documentProcess` on Linux and Windows return the same set of fields.

**What do 0, 1 and 2 mean in the result?**

It depends on the object. In `verification`, 0 = Pass, 1 = Fail, 2 = Not checked. In `imageQuality.checks`, 0 = Fail, 1 = Pass, 2 = Not checked.

**Why is the `security` object missing or not checked?**

Security checks need a license that includes authenticity, and authenticity must be set to `"normal"`. With `"none"`, security is skipped.

**Why is there no `images` array in my result?**

`images` is returned for recognition calls only. It is omitted on `documentLiveness`.

**Where are the authenticity check names listed?**

See [Document security check fields](document-security-check-fields.md).

### Related documentation

* [Document security check fields](document-security-check-fields.md) · [Glossary](../../help/resources/glossary.md)
* [ID Document Recognition Mobile SDK](recognition-and-liveness/mobile-sdk/) · [Server SDK](recognition-and-liveness/server-sdk/)
* [Request a License](../../help/request-a-license-and-support.md) · [Status codes](../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../getting-started/choose-a-product.md) · [SDK comparison](../../help/comparisons/)
