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

**Global Secure Access (GSA)** is a Microsoft Entra capability that provides identity-driven, conditional access to resources across cloud and on-premises networks. It enables fine-grained, policy-based access and reduces reliance on broad VPNs.

TL;DR: GSA uses **identity as the control plane** to secure access to apps and resources across network boundaries — instead of relying on network-perimeter controls (like VPNs) that grant broad network access.

---

## 2. What is Global Secure Access?

- **GSA** connects remote networks and resources to Entra so access is evaluated by **Conditional Access** and **session controls**.  
- It publishes internal apps, provides scoped access, and issues **short-lived credentials** instead of permanent network credentials.

TL;DR: GSA enforces **Conditional Access** and session controls for resources regardless of location — instead of applying coarse, location-based controls only when users are on the corporate network.

---

## 3. Remote networks — How they work

- Administrators register a **remote network** and associate one or more **connectors/gateways**.  
- Connectors run locally and create outbound TLS tunnels to the Entra control plane (no inbound ports needed).  
- Entra evaluates identity and device posture; approved sessions are proxied or tunneled to the resource.

TL;DR: **Connectors** create outbound tunnels so identity-driven policies are applied before traffic reaches internal resources — instead of opening inbound firewall ports or exposing resources directly to the internet.

---

## 4. When and why you would use Global Secure Access

When:
- Secure internal apps without public exposure.  
- Replace broad **VPN** access with **app-level** access.  
- Enforce consistent **Conditional Access** across all resources.

Why:
- Centralized policy, reduced attack surface, better UX, and easier operations.

TL;DR: Use GSA to apply identity- and policy-based controls and avoid exposing internal networks — instead of blanket VPN access or IP-based allowlists that trust network location rather than identity.

---

## 5. What GSA replaces or complements

- Replaces broad **VPNs** for many scenarios.  
- Complements or replaces legacy reverse proxies (WAP) and **Entra Application Proxy** depending on needs.  
- Shifts control from network perimeter to **identity and policy**.

TL;DR: GSA reduces reliance on network-level trust and emphasizes **identity-driven access** — instead of trusting IP addresses or network segments as proof of entitlement.

---

## 6. Create and configure remote networks (high-level)

Steps:
1. Plan scope: subnets/resources to expose.  
2. Deploy connectors/gateways in the remote network.  
3. Register the remote network in Entra and assign connectors.  
4. Configure DNS/routing.  
5. Create **Conditional Access** and session policies for the resources.  
6. Test and refine.

TL;DR: Plan → deploy connectors → register → apply policies → test — instead of centrally opening VPN tunnels and managing broad network access per user/device.

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

Below are concise, concrete use cases showing the problem, the GSA solution, why it helps, and short implementation steps.

1) Remote employee access to an internal web app (no VPN)
- Problem: VPN is heavy and grants broad access.  
- GSA solution: Deploy a **connector**, register the **remote network**, and apply a **Conditional Access** policy requiring MFA and device compliance for the app.  
- Why: Limits access to the app, enforces identity checks, avoids full network access.  
- Steps: deploy connector → register network → map app → create CA policy → test.

2) Scoped partner access to branch-office service
- Problem: Contractor needs access to a single service; VPN/domain accounts are inappropriate.  
- GSA solution: Register branch as a **remote network**, publish only the service, and create a policy scoped to partner accounts.  
- Why: Minimizes blast radius and provides auditability.  
- Steps: register branch → deploy connector → map resource → create partner-scoped CA.

3) Developer workflows without VPN
- Problem: Developers use VPN for build servers/databases (slow, risky).  
- GSA solution: Provide **short-lived, scoped access** to dev resources tied to identity and device posture.  
- Why: Better UX, no standing credentials.  
- Steps: map dev resources → create policy for dev group + compliant devices → enforce session limits.

4) Zero Trust segmentation for internal apps
- Problem: Lateral movement risk from broad network access.  
- GSA solution: Publish only specific apps via connectors and require **device compliance + MFA** per app.  
- Why: Enforces least privilege, reduces lateral attack paths.  
- Steps: identify critical apps → publish via connectors → apply per-app CA + session controls → monitor.

5) Temporary B2B collaboration (time-limited)
- Problem: External partner needs temporary access.  
- GSA solution: Invite partner via Entra B2B, map resource to remote network, and apply a time-bound policy.  
- Why: Temporary, auditable access without long-lived accounts.  
- Steps: invite guest → map resource → create time-bound CA policy → revoke after project.

6) Emergency break-glass with controls
- Problem: Need auditable emergency access when normal auth fails.  
- GSA solution: Configure break-glass accounts with strict approval and short sessions routed via a dedicated connector with logging.  
- Why: Preserves emergency access with accountability.  
- Steps: create break-glass process → approve workflow → restrict session duration and log activity.

7) Phased migration to retire VPN concentrators
- Problem: Large estate depends on VPN concentrators.  
- GSA solution: Migrate apps incrementally to remote networks and GSA connectors, with per-app policies until VPNs can be retired.  
- Why: Low-risk, phased approach.  
- Steps: pilot apps → publish via connector → validate policies → expand rollout → decommission VPN.

TL;DR: Use GSA for targeted, identity-driven access—common uses: remote user access, partner access, developer workflows, zero trust segmentation, temporary B2B access, emergency access, and phased VPN retirement — instead of granting users unrestricted VPN access to the entire network.

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
