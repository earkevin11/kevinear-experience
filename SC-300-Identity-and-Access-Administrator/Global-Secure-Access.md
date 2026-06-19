# Global Secure Access

> Notes and study guide for SC-300: Identity and Access Administrator — **Global Secure Access (GSA)**.

---

## Table of Contents

1. [Overview — Read This First](#1-overview--read-this-first)  
2. [What is Global Secure Access?](#2-what-is-global-secure-access)  
3. [Remote networks — How they work](#3-remote-networks--how-they-work)  
4. [When and why you would use Global Secure Access](#4-when-and-why-you-would-use-global-secure-access)  
5. [What GSA replaces or complements](#5-what-gsa-replaces-or-complements)  
6. [Create and configure remote networks (high-level)](#6-create-and-configure-remote-networks-high-level)  
7. [Key concepts and components](#7-key-concepts-and-components)  
8. [Practical scenarios and examples](#8-practical-scenarios-and-examples)  
9. [Resources and further reading](#9-resources-and-further-reading)  
10. [Glossary](#10-glossary)

---

## 1. Overview — Read This First

**Global Secure Access (GSA)** is a Microsoft Entra capability that provides identity-driven, conditional access to resources across cloud and on-premises networks. It enables fine-grained, policy-based access and reduces reliance on broad **VPNs**.

TL;DR: GSA uses **identity as the control plane** to secure access to apps and resources across network boundaries — instead of relying on network-perimeter controls (like VPNs) that grant broad network access to everything behind the tunnel.

---

## 2. What is Global Secure Access?

- **GSA** connects remote networks and resources to Entra so access is evaluated by **Conditional Access** and **session controls**.  
- It publishes internal apps, provides scoped access, and issues **short-lived credentials** instead of permanent network credentials.

TL;DR: GSA enforces **Conditional Access** and **session controls** for resources regardless of location — instead of applying coarse, location-based controls only when users are on the corporate network.

---

## 3. Remote networks — How they work

- Administrators register a **remote network** and associate one or more **connectors/gateways**.  
- Connectors run locally and create outbound TLS tunnels to the Entra control plane (no inbound ports needed).  
- Entra evaluates identity and device posture; approved sessions are proxied or tunneled to the resource.

TL;DR: **Connectors** create outbound tunnels so identity-driven policies are applied before traffic reaches internal resources — instead of opening inbound firewall ports or exposing services directly to the internet.

---

## 4. When and why you would use Global Secure Access

When:
- Secure internal apps without public exposure.  
- Replace broad **VPN** access with **app-level** access.  
- Enforce consistent **Conditional Access** across all resources.

Why companies adopt GSA (benefits):
- **Reduce attack surface** — avoid exposing services or opening inbound ports.  
- **Centralize policy & compliance** — Conditional Access and session controls applied uniformly.  
- **Improve user experience** — SSO and scoped access avoid heavy VPNs.  
- **Lower operational cost** — fewer VPN concentrators, fewer appliances to manage.  
- **Better auditability** — per-resource access logs tied to identities.

TL;DR: Use GSA to apply identity- and policy-based controls and avoid exposing internal networks — instead of blanket **VPN** access or IP-based allowlists that treat network location as proof of entitlement.

---

## 5. What GSA replaces or complements

- Replaces broad **VPNs** for many scenarios.  
- Complements or replaces legacy reverse proxies (WAP) and **Entra Application Proxy** depending on needs.  
- Shifts control from network perimeter to **identity and policy**.

TL;DR: GSA reduces reliance on network-level trust and emphasizes **identity-driven access** — instead of trusting IP addresses, subnets, or VPN presence as sufficient for access.

---

## 6. Create and configure remote networks (high-level)

Steps (summary):
1. Plan scope: subnets/resources to expose.  
2. Deploy connectors/gateways in the remote network.  
3. Register the remote network in Entra and assign connectors.  
4. Configure DNS/routing.  
5. Create **Conditional Access** and session policies for the resources.  
6. Test and refine.

TL;DR: Plan → deploy connectors → register → apply policies → test — instead of centrally opening and managing per-user/per-device VPN tunnels for broad network access.

---

## 7. Key concepts and components

- **Connector / Gateway**: outbound TLS tunnel and traffic forwarder.  
- **Remote network**: on-prem or private network registered with Entra.  
- **Conditional Access**: policy engine (MFA, device posture, risk).  
- **Session controls**: post-auth monitoring and restrictions.  
- **Short-lived tokens**: temporary credentials for approved sessions.

TL;DR: Remember **connectors**, **Conditional Access**, **session controls**, and **short-lived tokens** — instead of long-lived credentials, static ACLs, and flat network access.

---

## 8. Practical scenarios and examples

Most commonly used cases and emphasized why companies choose GSA and the benefits.

1) Remote employee access to internal apps (most common)
- Problem: VPN gives broad network access and poor UX.  
- GSA solution: Publish the app via a **connector** and require **Conditional Access** (MFA + device compliance).  
- Company benefits: **Reduced attack surface**, **centralized control**, better UX and faster onboarding.  
- Quick steps: deploy connector → register network → map app → create CA policy → test.

2) Scoped partner/B2B access (common)
- Problem: Contractors/partners need limited access; creating accounts or VPNs is risky.  
- GSA solution: Use Entra B2B guest accounts + a connector + a resource-scoped Conditional Access policy.  
- Company benefits: **Least privilege**, audit trails, and time-bound access for compliance.  
- Quick steps: invite guest → map resource → apply scoped CA policy → monitor and revoke.

3) Zero Trust segmentation for critical apps (strategic)
- Problem: Lateral movement risk from flat network trust and VPNs.  
- GSA solution: Publish only required apps via connectors and enforce per-app **Conditional Access** + session controls.  
- Company benefits: **Least privilege**, reduced lateral attack surface, and simpler compliance evidence.  
- Quick steps: prioritize apps → publish via connectors → enforce per-app CA and session controls → monitor logs.

4) Developer and automation workflows (operational)
- Problem: Devs use VPN and long-lived credentials to access build systems and databases.  
- GSA solution: Provide **short-lived, scoped access** tied to identity and device posture.  
- Company benefits: better security hygiene, fewer standing credentials, and faster CI/CD workflows.  
- Quick steps: map dev resources → create policies for dev groups and compliant devices → enforce session limits and logging.

TL;DR: These four scenarios—remote employees, partner/B2B, zero-trust segmentation, and developer workflows—cover the most common business drivers for adopting GSA: **reduce attack surface, centralize policy, improve UX, and lower operational cost** — instead of continuing with broad VPN access and static network trust.

---

## 9. Resources and further reading

- Microsoft Learn: Deploy and configure Microsoft Entra Global Secure Access — Create remote networks  
  https://learn.microsoft.com/en-us/training/modules/deploy-configure-microsoft-entra-global-secure-access/6-create-remote-networks  
- Microsoft Docs: Global Secure Access overview  
  https://learn.microsoft.com/en-us/entra/global-secure-access/

TL;DR: Start with the Microsoft Learn module and official docs — instead of relying on unofficial or outdated third-party summaries.

---

## 10. Glossary

- **GSA** — Global Secure Access  
- **Connector** — software that connects a remote network to Entra  
- **Remote network** — on-prem or private network registered for access  
- **Conditional Access** — Entra policy engine  
- **Session controls** — post-auth monitoring/restrictions

TL;DR: Use this glossary for quick reference — instead of guessing terminology or mixing up components.

---

*Last updated: 2026 — Personal study notes for SC-300*
