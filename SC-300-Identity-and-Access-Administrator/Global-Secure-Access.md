# Global Secure Access

> Notes and study guide for SC-300: Identity and Access Administrator — **Global Secure Access (GSA)**.

---

## Table of Contents

1. [Overview — Read This First](#1-overview--read-this-first)  
2. [What is Global Secure Access?](#2-what-is-global-secure-access)  
3. [Remote networks — How they work](#3-remote-networks--how-they-work)  
4. [How VPNs and network-perimeter controls work](#4-how-vpns-and-network-perimeter-controls-work)
5. [When and why you would use Global Secure Access](#5-when-and-why-you-would-use-global-secure-access)  
6. [What GSA replaces or complements](#6-what-gsa-replaces-or-complements)  
7. [Create and configure remote networks (high-level)](#7-create-and-configure-remote-networks-high-level)  
8. [Key concepts and components](#8-key-concepts-and-components)  
9. [Practical scenarios and examples](#9-practical-scenarios-and-examples)  
10. [Sample Conditional Access policy](#10-sample-conditional-access-policy)
11. [Architecture diagrams — GSA vs VPN](#11-architecture-diagrams--gsa-vs-vpn)
12. [Resources and further reading](#12-resources-and-further-reading)  
13. [Glossary](#13-glossary)

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

**What is a remote network?**
- A **remote network** is any network that hosts resources accessed by users but is not the user's immediate endpoint network. Typical examples:
  - An **on‑prem datacenter** or branch office subnet hosting internal web apps, file servers, or APIs.
  - A **private VNet** in the cloud (Azure, AWS) that contains line-of-business services.
  - A partner-managed network segment or co-lo environment where company apps run.
  - Environments behind a corporate firewall reachable through a connector or gateway.

**Essential properties of remote networks**
- Hosts resources that are not publicly published and require controlled access.
- Has local infrastructure capable of running a **connector/gateway** (VM or appliance) or allowing a connector to reach internal resources.
- Supports outbound TLS connections to the Entra control plane (no inbound firewall holes required).

**How remote networks integrate with GSA**
1. Register the remote network in Entra and associate one or more **connectors**.  
2. Deploy connectors/gateways in the network; they establish outbound, **TLS-encrypted** tunnels to Microsoft.  
3. Map internal resources (hostnames/IPs) to the remote network in Entra so Entra knows where to forward approved traffic.  
4. Create **Conditional Access** and session policies scoped to those resources and users.  
5. When a user requests access, Entra evaluates identity and device posture; approved sessions are proxied/tunneled via the connector to the target.

**Security and operational considerations**
- **Outbound-only connectors**: avoid opening inbound firewall ports; connectors initiate secure outbound connections.  
- **DNS & routing**: ensure connectors can resolve and route to target resources; consider split-horizon DNS.  
- **High availability**: deploy multiple connectors for redundancy and scale.  
- **Logging & monitoring**: enable connector logs and Entra sign-in/session logs for audit and incident response.  
- **Least privilege**: map only the required apps/services, not entire subnets, to minimize the attack surface.

TL;DR: **Remote networks** are on‑prem or private cloud segments that host private resources; GSA uses **connectors** to establish outbound tunnels so Entra can apply identity‑driven policies before traffic reaches those resources — instead of exposing services or relying on broad VPN access.

---

## 4. How VPNs and network-perimeter controls work

This section explains VPNs and perimeter-based trust in plain language for someone new to networking.

What is a VPN?
- A **VPN (Virtual Private Network)** creates an encrypted tunnel between a user's device (or a remote site) and a corporate network so the remote endpoint appears to be on the corporate network. Think of the VPN tunnel as a private pipe from the user into the company.

How access decisions are typically made with a VPN
- When a user connects via VPN, the network authenticates the connection (often with username/password, certificates, or an MFA step). Once the tunnel is established, the user's traffic is sent into the corporate network.
- Most traditional VPN setups rely on **network-perimeter controls**: once the tunnel is open, the user inherits network-level access. The VPN assumes the user is trusted and grants access to whatever resources the network allows the user's IP to reach.

Does the VPN automatically allow access to everything?
- Short answer: Not always, but often yes in practice. In many organizations, the VPN places the client on the corporate subnet or assigns an IP that is allowed to reach many internal services. Access to specific apps may still be controlled by application-level checks, but the VPN commonly removes network restrictions, enabling broad lateral access by default.
- This is why VPNs are sometimes called "broad access" solutions: they move the user behind the corporate firewall where network-level trust is high.

Limitations and risks of perimeter-based VPN trust
- **Broad attack surface**: a compromised device with VPN access can reach many systems — more opportunity for lateral movement.  
- **Coarse trust model**: the network trusts the tunnel, not the identity or device posture of the user for each app.  
- **Operational overhead**: scaling VPN concentrators, managing IP pools, and maintaining firewall rules is complex and costly.  
- **Poor UX**: VPNs can be slow, require client software, and create friction for users.

Why companies are moving away from pure perimeter/VPN trust
- Organizations want **least-privilege**, identity-based access that enforces MFA, device posture, and per-app policies — not an all-or-nothing tunnel. GSA enables these controls so access is granted per resource based on identity and signals, rather than because the user is "on the network."

TL;DR: A VPN creates an encrypted tunnel that can place a user inside the corporate network; once connected, many organizations implicitly trust that connection and allow broad access — instead of evaluating access per app based on identity, device posture, and risk.

---

## 5. When and why you would use Global Secure Access

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

## 6. What GSA replaces or complements

- Replaces broad **VPNs** for many scenarios.  
- Complements or replaces legacy reverse proxies (WAP) and **Entra Application Proxy** depending on needs.  
- Shifts control from network perimeter to **identity and policy**.

TL;DR: GSA reduces reliance on network-level trust and emphasizes **identity-driven access** — instead of trusting IP addresses, subnets, or VPN presence as sufficient for access.

---

## 7. Create and configure remote networks (high-level)

Steps (summary):
1. Plan scope: subnets/resources to expose.  
2. Deploy connectors/gateways in the remote network.  
3. Register the remote network in Entra and assign connectors.  
4. Configure DNS/routing.  
5. Create **Conditional Access** and session policies for the resources.  
6. Test and refine.

TL;DR: Plan → deploy connectors → register → apply policies → test — instead of centrally opening and managing per-user/per-device VPN tunnels for broad network access.

---

## 8. Key concepts and components

- **Connector / Gateway**: outbound TLS tunnel and traffic forwarder.  
- **Remote network**: on-prem or private network registered with Entra.  
- **Conditional Access**: policy engine (MFA, device posture, risk).  
- **Session controls**: post-auth monitoring and restrictions.  
- **Short-lived tokens**: temporary credentials for approved sessions.

TL;DR: Remember **connectors**, **Conditional Access**, **session controls**, and **short-lived tokens** — instead of long-lived credentials, static ACLs, and flat network access.

---

## 9. Practical scenarios and examples

I pruned the scenarios to the most commonly used cases and emphasized why companies choose GSA and the benefits.

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

TL;DR: These four scenarios—remote employees, partner/B2B, zero-trust segmentation, and developer workflows—cover the most common business drivers for adopting GSA: **reduce attack surface, c[...]

---

## 10. Sample Conditional Access policy

Below is a concise, practical example you can implement in the Entra portal for the "Remote app access" scenario. Use this as a template and tailor to your organization's user groups and apps.

Portal steps (recommended):
1. Sign in to the Azure portal → Entra ID → Security → Conditional Access → New policy.  
2. Name: **GSA - Remote App Access**.  
3. Assignments → Users: select the target user group (e.g., "All employees" or "HR-app-users").  
4. Cloud apps: select the published internal app (or the enterprise app representing it).  
5. Conditions → Locations: exclude trusted corporate IPs (if applicable).  
6. Conditions → Device platforms / Client apps: configure as needed (block legacy auth).  
7. Access controls → Grant: **Require multi-factor authentication** AND **Require device to be marked compliant**.  
8. Session controls → Sign-in frequency or Persistent browser session: configure per policy.  
9. Enable policy in report-only first, review sign-ins, then turn on.

Example (pseudo-JSON for clarity — verify in your tenant and Graph API docs before automating):
{
  "displayName": "GSA - Remote App Access",
  "state": "enabled",
  "assignments": {
    "users": { "includeGroups": ["HR-app-users"] },
    "cloudApps": { "include": ["<app-id-or-name>"] }
  },
  "conditions": {
    "locations": { "exclude": ["TrustedCorpIPs"] },
    "clientAppTypes": { "include": ["browser","mobileAppsAndDesktopClients"] }
  },
  "grantControls": {
    "operator": "AND",
    "builtInControls": ["mfa","requireDeviceCompliance"]
  },
  "sessionControls": { "signInFrequency": { "value": 8, "type": "hours" } }
}

Notes:
- Test in **Report-only** mode first.  
- Use **named locations** and **device compliance** signals to avoid locking out legitimate users.  
- Consider excluding emergency break-glass accounts from Conditional Access, but protect those accounts with strict audits and short lifetimes.

TL;DR: Create a policy that targets the app and user group, requires **MFA + device compliance**, and test in report-only before enabling — instead of granting access based solely on network presence (VPN/IP).

---

## 11. Architecture diagrams — GSA vs VPN

### ASCII: GSA branch-office + remote user
```
                    Microsoft Entra / Global Secure Access
                               |        ▲
          ---------------------------------------------
          |                                           |
      Branch Office                             Remote User
          |                                           |
      Firewall                                  GSA Client
          |                                           |
       Connector                                 Device Tunnel
          |                                           |
    Internal App(s)  <------  Entra Control Plane  ------>  Microsoft 365
```
**Summary:** This diagram shows a GSA deployment where **Entra evaluates identity and policies** before instructing an on‑prem **connector** to forward approved sessions to internal apps. Remote users authenticate to Entra (MFA/device posture) and device tunnels or browser sessions are proxied only for authorized resources, limiting exposure compared to broad network access.

### ASCII: VPN-based architecture (traditional)
```
                Corporate Network Perimeter / VPN Concentrator
                                 ▲
                ----------------- | ------------------
                |                                   |
           Branch Office                         Remote User
                |                                    |
         Internal App(s) <-- Firewall/LAN --> VPN Gateway (tunnel)
                |                                    |
            On-prem resources                   VPN Client (tunnel)
```
**Summary:** This traditional VPN diagram shows that once a VPN **tunnel** is established, the remote device is effectively placed onto the corporate LAN and can reach internal resources permitted by network routing and firewall rules. This model often results in **network-level trust** that grants broad access and increases lateral movement risk.

### PlantUML: GSA flow (copy to PlantUML editor)
@startuml
title GSA — Branch + Remote User
node "Remote User\n(Browser / Device)" as User
node "Entra / Control Plane" as Entra
node "Connector\n(Branch Office)\nOutbound TLS" as Connector
node "Internal App(s)\n(Datacenter / VNet)" as App
node "Microsoft 365" as M365

User --> Entra : Sign-in / Conditional Access
Entra --> Connector : Approve & instruct forwarding
User --> Connector : Device tunnel / proxied traffic
Connector --> App : Forward approved session
Entra --> M365 : Cloud services (SSO, policies)
@enduml

**Summary:** The PlantUML GSA flow illustrates the same concept in a sequence: user authenticates to **Entra**, **Conditional Access** evaluates the request, Entra instructs the **connector** to proxy the session, and the connector forwards traffic only to the mapped internal app. This enforces per-resource access controls.

### PlantUML: VPN flow (copy to PlantUML editor)
@startuml
title VPN — Branch + Remote User
node "Remote User\n(Browser / Device)" as User
node "VPN Gateway / Concentrator" as VPNGW
node "Internal App(s)\n(Datacenter / VNet)" as App
User --> VPNGW : Authenticate & establish tunnel
VPNGW --> App : Route traffic into corporate LAN
@enduml

**Summary:** The PlantUML VPN flow emphasizes the VPN gateway establishing a tunnel that routes traffic into the corporate LAN — once up, the client can reach internal apps based on network routing/firewall rules, rather than per-app identity checks.

TL;DR: GSA places **identity and policy** at the center and proxies sessions per resource via connectors; VPN places users onto the corporate network where network-level trust often yields broad access.

---

## 12. Resources and further reading

- Microsoft Learn: Deploy and configure Microsoft Entra Global Secure Access — Create remote networks  
  https://learn.microsoft.com/en-us/training/modules/deploy-configure-microsoft-entra-global-secure-access/6-create-remote-networks  
- Microsoft Docs: Global Secure Access overview  
  https://learn.microsoft.com/en-us/entra/global-secure-access/

TL;DR: Start with the Microsoft Learn module and official docs — instead of relying on unofficial or outdated third-party summaries.

---

## 13. Glossary

- **GSA** — Global Secure Access  
- **Connector** — software that connects a remote network to Entra  
- **Remote network** — on-prem or private network registered for access  
- **Conditional Access** — Entra policy engine  
- **Session controls** — post-auth monitoring/restrictions

TL;DR: Use this glossary for quick reference — instead of guessing terminology or mixing up components.

---

*Last updated: 2026 — Personal study notes for SC-300*
