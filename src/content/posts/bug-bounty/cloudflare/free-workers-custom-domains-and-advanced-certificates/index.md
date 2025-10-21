---
title: 'ประสบการณ์แจ้ง(ที่คิดว่า)บัคของเว็บ Cloudflare ครั้งแรก'
published: 2025-10-22
description: 'แชร์ประสบการณ์การแจ้งช่องโหว่ ที่ตัวเองคิดว่าใช่ แต่...'
image: 'flow.png'
tags: ['Bug Bounty']
category: 'Experience'
draft: false
language: 'en'
---

# Free Workers Custom Domains & Advanced Certificates – What’s Really Happening?

If you’ve ever added a **Custom Domain** to a Cloudflare Worker on a Free plan, you might have noticed something curious: Cloudflare seems to issue an **Advanced Edge Certificate** automatically.

Wait… isn’t that a paid feature? Let’s break down what’s going on.

---

## How It Works

When you add a custom domain to a Worker:

1. Cloudflare automatically provisions a certificate for your hostname.
2. The certificate is issued via **Google Trust Services (GTS)**, valid for ~3 months.
3. Even on Free plans (Universal SSL only), the certificate appears to be an “Advanced” type.

The important thing to understand: **this behavior is intended.**
Cloudflare documents this in [Workers Custom Domains – Certificates](https://developers.cloudflare.com/workers/configuration/routing/custom-domains/#certificates):

> “Creating a Custom Domain will also generate an Advanced Certificate on your target zone for your target hostname.”

---

## What Happens After You Remove a Custom Domain?

Another interesting point: if you remove/unbind the custom domain from the Worker:

- The certificate **remains active** in SSL/TLS → Edge Certificates.
- Cloudflare continues **auto-renewal** until the certificate is deleted manually.
- This may seem like a “lingering” or orphaned certificate, but it **does not give anyone unauthorized access** — it’s tied to your zone.

---

## Why You Can’t Just Abuse This for Free Advanced Certificates

While technically you could script subdomain creation and automatic certificate issuance:

- You must **control the zone**.
- API **rate limits**, CA issuance limits, and Cloudflare’s **abuse detection** prevent mass exploitation.
- This flow doesn’t bypass entitlements or give access to other users’ certificates.

So, it’s not a security vulnerability — it’s just **how the system is designed**.

---

## Advanced Certificate Manager (ACM) vs Workers Flow

- **Free Workers Flow:**
  - Provides automatic HTTPS for custom domains.
  - Covers most development needs.
- **Advanced Certificate Manager (paid)** allows:
  - Supports wildcards across multiple levels (e.g., `*.*.example.com`)
  - Allows custom SANs and CA selection.
  - Lets you set custom certificate durations.
  - Provides enterprise-level certificate lifecycle control.

In short, ACM is meant for organizations or SaaS providers who need more control. Free Workers flow is sufficient for most standard use cases.

---

## Key Takeaways

1. Free plan Workers automatically get advanced-like certificates — it’s **intended**.
2. Certificates remain active after removing the Worker binding — managed lifecycle, not a security issue.
3. Automation of subdomain creation is possible, but **throttled and limited**, so it’s not exploitable for free unlimited certs.
4. Paid ACM is about **control and customization**, not basic HTTPS.

---

### Closing Thoughts

Observing this behavior was an interesting learning experience:

- It clarified the boundary between **free entitlements** and **paid features**.
- It shows how Cloudflare manages certificates and auto-renewal behind the scenes.
- And it makes for a good example when discussing **certificate lifecycle, entitlement design, and automation limits** in blog posts or security research write-ups.

---

*Author’s Note:* I initially thought this might be a security issue, but after consulting the docs and Cloudflare’s HackerOne team, it turns out this is **expected behavior** — a reminder to always check documentation before assuming a bug.
