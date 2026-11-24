---
description: >-
  Shared Faceplugin HTTP endpoints. GET /api/health, /api/machinecode,
  /api/backend, /api/licenseStatus, and POST /api/activate. These routes use a
  JSON envelope.
icon: share-nodes
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

# Shared endpoints

Every shipping Linux and Windows Faceplugin app implements the same **shared** routes.

Call these first. Confirm the process is up. Copy the machine code. Then activate.

A **machine code** is a string that identifies the server or container for licensing.

Process routes (`/api/detect`, `/api/liveness`, `/api/documentProcess`, and so on) are on the product API pages. Those POSTs return **raw engine JSON**. They do not use the envelope below.

### `GET /api/health`

Check that the container or `run.bat` process is listening. **No license is required.**

```bash
curl -s http://127.0.0.1:8083/api/health
```

|                  |                                    |
| ---------------- | ---------------------------------- |
| **Input**        | None                               |
| **Return value** | Envelope. `data.status` is `"ok"`. |

### `GET /api/machinecode`

Get the machine code for a server license. Send the returned `FPMC1.…` string to Faceplugin.

|                  |                                            |
| ---------------- | ------------------------------------------ |
| **Input**        | None                                       |
| **Return value** | Envelope. `data.machinecode` is `FPMC1.…`. |

{% hint style="warning" %}
A Docker container and the Linux host have **different** machine codes. If you run in Docker, send the code from the **container**.
{% endhint %}

### `GET /api/backend`

Reports which compute backend the app uses.

|                  |                                                                                                                          |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **Input**        | None                                                                                                                     |
| **Return value** | Envelope. Face Recognition and Face Liveness return `"backend": "cpu"`. Document products may read `FACEPLUGIN_BACKEND`. |

Example Document Reader `data`:

```json
{
  "product": "DocumentReader",
  "sdk_version": "1.0.0",
  "backend": "cpu"
}
```

### `POST /api/activate`

Unlocks product endpoints. On success the app also calls `init_sdk()`.

{% tabs %}
{% tab title="JSON / text" %}
Plain `FP1.…` text, or JSON `{"license":"FP1.…"}`.
{% endtab %}

{% tab title="form-data" %}
Field `license` as text, or a license file part named `license`.
{% endtab %}
{% endtabs %}

|                  |                                                                                                                                               |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| **Input**        | License key or file                                                                                                                           |
| **Return value** | Envelope. Success: `code` 0, `"Successfully activated"`, `data.activated` true. Failure: `code` -1, `"Invalid license"` or `"Empty license"`. |

### `GET /api/licenseStatus`

Every Face and Document Linux/Windows app implements this route. **No license is required** to call it.

|                  |                                               |
| ---------------- | --------------------------------------------- |
| **Input**        | None                                          |
| **Return value** | Envelope. `data` is the license object below. |

**Face products** (`recognition` / `liveness`):

```json
{
  "licensed": true,
  "level": 2,
  "levelName": "Developer",
  "recognition": true,
  "liveness": true,
  "label": "Recognition + Liveness"
}
```

* Face Recognition only: `liveness` is always `false`.
* Face Liveness only: `recognition` is always `false`.
* Face Recognition + Liveness packages: both flags come from the license level (`0` recognition, `1` liveness, `2+` both).

**Document products** (`recognition` / `authenticity`):

```json
{
  "licensed": true,
  "level": 2,
  "levelName": "Developer",
  "recognition": true,
  "authenticity": true,
  "label": "Recognition + Liveness"
}
```

### `OPTIONS /api/<path>`

Returns HTTP 204 for CORS preflight.

### Errors

Unhandled exceptions return envelope `code` **-19** and HTTP 500. Unknown paths return `code` **-10** and HTTP 404. See [Status codes](../../help/resources/status-codes.md).

### Related documentation

* [HTTP API overview](./) · [Request a License](../../help/request-a-license-and-support.md)
