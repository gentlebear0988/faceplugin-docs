---
description: >-
  Request a Faceplugin offline license. Mobile license keys bind to app id.
  Server licenses start from a machine code from GET /api/machinecode. Docker
  and host codes differ. On-premise, no per-call clo
icon: key
---

# Request a License & Support

Faceplugin licenses are **offline**. After activation, face matching, liveness, and document OCR do **not** need the internet.

In short: **mobile** **license keys** bind to your app id; **server** licenses start from a **machine code** from the host or Docker you will run in production. Definitions: [Glossary](resources/glossary.md).

### Need a license?

* **Mobile SDK:** contact us via WhatsApp, Telegram, or email. Demo license keys are bound to the **sample application id / bundle id** on each platform page. Request a new license key if you use your own app.
* **Server SDK (Linux / Windows / Docker):** start the API once, copy the **machine code** from logs or `GET /api/machinecode`, and send that code. Docker and a native host have **different** machine codes. Use the code from the environment you will run in production.

Do not paste demo license keys into your production app.

### FAQ

**Can Faceplugin run completely offline?**&#x20;

Yes, after the key is issued and activated.

**Why is my Docker machine code different from the host?**

The fingerprint includes Docker. License the environment you ship.

**Status codes 1–4 on mobile?**

See [Status codes](resources/status-codes.md).

### Need support?

Faceplugin provides integration help for licensed SDKs, plus ongoing support after you go live. Email, WhatsApp, or Telegram with your OS, GitHub repo name, activate/init result codes, and whether you use Docker or a native Google Drive runtime.

### Contact us

* Email : [info@faceplugin.com](mailto:info@faceplugin.com)
* WhatsApp : [+1 (469) 278-4822](https://wa.me/+14692784822)
* Telegram : [@FacePluginSupport](https://t.me/FacePluginSupport)

### Related documentation

* [Try it](../getting-started/try-it.md) · [HTTP API shared endpoints](../integration/http-api/shared.md) · [Production deployment](../integration/deploy-and-host/production-deployment.md)
* [FAQ](resources/faq.md) · [Glossary](resources/glossary.md) · [Troubleshooting](resources/troubleshooting.md)
* [Choose a product](../getting-started/choose-a-product.md) · [Changelog](resources/changelog.md)

{% hint style="info" %}
GitBook admin (not this markdown): custom domain, Open Graph image, sitemap, and **Google Search Console** on the docs hostname.
{% endhint %}
