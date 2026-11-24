---
description: >-
  Faceplugin document security check fields for RGB captures. JSON keys and
  titles returned in security.pages: photoOriginAnalysis, hologramIntegrity,
  physicalDocument.
icon: shield-halved
---

# Document security check fields

When authenticity is **on**, `recognize` / `documentProcess` add a `security` object. Faceplugin Document Reader accepts **RGB** stills (phone camera, gallery, or a color JPEG/PNG). UV, IR, and axial-light checks are not returned.

Each check object includes a `title` you can show in the UI and a `result` (`success`, `fail`, or `notChecked`). `securityPattern` uses `score` (percent) instead of `result`. Not every document produces every key.

```json
{
  "security": {
    "overall": "success",
    "label": "Authentic",
    "pages": [
      {
        "pageIndex": 0,
        "overall": "success",
        "label": "Authentic",
        "photoOriginAnalysis": {
          "title": "Photo origin analysis",
          "result": "success",
          "checks": {
            "photoIntegrity": {
              "title": "Photo authenticity",
              "result": "success"
            }
          }
        }
      }
    ]
  }
}
```

### Page-level keys

Skip these when listing checks: `pageIndex`, `overall`, `label`. Front is `pageIndex` **0**, back **1**.

| Key                   | Title                                   |
| --------------------- | --------------------------------------- |
| `physicalDocument`    | Physical document verification          |
| `photoOriginAnalysis` | Photo origin analysis                   |
| `securityPattern`     | Security pattern analysis               |
| `barcodeFormat`       | Barcode format verification             |
| `portraitMatch`       | Portrait consistency verification       |
| `portraitRegion`      | Portrait area verification              |
| `opticallyVariable`   | Optically variable feature verification |
| `hologramIntegrity`   | Hologram integrity verification         |
| `kineticFeature`      | Kinetic optical verification            |
| `letterpressPattern`  | Letterpress pattern verification        |
| `textCrosscheck`      | Text consistency analysis               |
| `mrzCrosscheck`       | MRZ consistency analysis                |

### Nested `checks` keys

Present only when that sub-check ran. Each value is `{ "title", "result" }`.

| Key                 | Title                             |
| ------------------- | --------------------------------- |
| `blankArea`         | Unused area                       |
| `fillArea`          | Printed background                |
| `photoArea`         | Document photo                    |
| `mrzArea`           | Machine-readable zone             |
| `hologramPresent`   | Hologram presence                 |
| `hologramStill`     | Still hologram                    |
| `hologramAngles`    | Multi-angle hologram              |
| `hologramMotion`    | Moving hologram                   |
| `patternIntact`     | Unbroken pattern                  |
| `patternAligned`    | Pattern alignment                 |
| `patternColor`      | Pattern color match               |
| `patternWeight`     | Pattern line thickness            |
| `photoSize`         | Photo size                        |
| `photoIntegrity`    | Photo authenticity                |
| `photoColor`        | Photo color                       |
| `photoShape`        | Photo shape                       |
| `photoCorners`      | Photo corners                     |
| `portraitVsGhost`   | Photo vs ghost image              |
| `portraitVsPrint`   | Photo vs printed portrait         |
| `portraitVsLive`    | Photo vs live face                |
| `portraitVsBarcode` | Photo vs barcode image            |
| `barcodeArea`       | Barcode area                      |
| `barcodeSize`       | Barcode size                      |
| `barcodePattern`    | Barcode pattern verification      |
| `ghostImage`        | Ghost portrait                    |
| `ghostClarity`      | Ghost image clarity               |
| `barcodeVsLive`     | Barcode vs live face              |
| `barcodeVsGhost`    | Barcode vs ghost image            |
| `ghostVsLive`       | Ghost vs live face                |
| `extraVsPrint`      | Extra photo vs printed portrait   |
| `extraVsLive`       | Extra photo vs live face          |
| `extraVsBarcode`    | Extra photo vs barcode            |
| `extraVsGhost`      | Extra photo vs ghost              |
| `displayAttack`     | Display attack detection          |
| `digitalSource`     | Digital source detection          |
| `digitalSeal`       | Digital signature                 |
| `opticalInk`        | Optical ink verification          |
| `laserInk`          | Laser-etched ink                  |
| `hiddenFeature`     | Hidden feature verification       |
| `contactChip`       | Contact chip                      |
| `microprint`        | Microprint                        |
| `printedText`       | Printed text                      |
| `depthAnalysis`     | 3D depth analysis                 |
| `faceDetection`     | Face verification                 |
| `faceLandmarks`     | Face landmarks                    |
| `noFace`            | Restricted area is empty          |
| `headPose`          | Head position                     |
| `documentGeometry`  | Document geometry analysis        |
| `monochromeCopy`    | Monochrome reproduction detection |
| `variablePrint`     | Variable print verification       |
| `ageMatch`          | Age consistency                   |
| `sexMatch`          | Sex consistency                   |
| `lowContrast`       | Low-contrast element              |

{% hint style="info" %}
A missing key means that check was not run for this image — not that it passed.
{% endhint %}

Top-level recognize contract: [Document result JSON](document-result-json.md).

### Related documentation

* [Document result JSON](document-result-json.md) · [Glossary](../../help/resources/glossary.md)
* [ID Document Liveness SDK](id-document-liveness-sdk/)
* [ID Document Recognition Server SDK](recognition-and-liveness/server-sdk/)
* [Request a License](../../help/request-a-license-and-support.md) · [Status codes](../../help/resources/status-codes.md)
* [Combining products (eKYC)](../../getting-started/choose-a-product.md) · [SDK comparison](../../help/comparisons/)
