# Global Secure Access

> Notes and study guide for SC-300: Identity and Access Administrator — Global Secure Access (GSA).

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

Global Secure Access (GSA) is a Microsoft Entra capability that simplifies secure, identity-aware connectivity from users and devices to enterprise resources — whether those resources are in the cloud or on-premises. GSA helps enforce consistent access controls, reduce reliance on traditional network VPNs, and enable conditional, least-privilege access.

TL;DR: GSA uses identity as the control plane to provide secure access to apps and resources across network boundaries, reducing the need for broad VPNs.

This document summarizes what GSA is, how remote networks work with it, when and why to use it, what legacy technologies it replaces or complements, and links to Microsoft Learn for step-by-step configuration.

---

## 2. What is Global Secure Access?

- GSA is part of Microsoft Entra (formerly Azure AD) capabilities focused on secure access to resources.
- It provides identity-driven access controls and a way to connect remote networks to Entra so conditional access, policy evaluation, and session controls can be applied consistently.
- GSA can be used to publish internal apps, provide secure access without full network-level VPN, and integrate with enforcement points that evaluate device and user signals.

TL;DR: GSA is an Entra feature that connects remote networks and enforces Conditional Access and session controls for resources regardless of network location.

In short: GSA lets organizations provide secure, conditional access to apps and resources across network boundaries using identity as the control plane.

---

## 3. Remote networks — How they work

Remote networks in the context of GSA are network endpoints (for example, an on-prem datacenter network or a branch office) that are connected to Microsoft Entra so that Entra can route access requests and apply policies when users attempt to reach resources in that network.

High-level flow:

1. An administrator registers/configures a remote network in Entra and associates it with one or more network connectors or gateways.
2. Connectors or gateways run in the remote network (on-premises or in a VNet) and establish secure outbound tunnels to Microsoft's control plane — these are typically TLS-encrypted, outbound-only connections to avoid inbound firewall holes.
3. When a user or device attempts to access a resource that lives in the remote network, Entra evaluates the sign-in against Conditional Access, device posture, and session policies.
4. If allowed, Entra coordinates the connection (or issues a short-lived credential/token) and traffic is forwarded via the connector/gateway to the resource.

Important properties:
- Connectors initiate outbound connections, simplifying firewall/NAT traversal.
- Access decisions are identity- and policy-driven (not simply based on network location).
- Traffic paths can be proxied or tunneled depending on configuration (application proxy-style, or network-level tunnels for managed connectivity).

TL;DR: Remote networks use local connectors that create outbound tunnels to Entra so identity-driven policies can be applied before traffic reaches internal resources.

For step-by-step guidance on creating remote networks, see Microsoft's module: Create remote networks
https://learn.microsoft.com/en-us/training/modules/deploy-configure-microsoft-entra-global-secure-access/6-create-remote-networks

---

## 4. When and why you would use Global Secure Access

When to use GSA:
- You want to provide secure access to on-premises or private cloud apps without exposing them directly to the public internet.
- You want to reduce or replace broad VPN access with identity-aware, least-privilege access to specific apps or services.
- You need consistent Conditional Access enforcement for resources regardless of their network location.
- You want simplified operations where connectors/gateways make secured outbound connections and avoid opening inbound ports.

Why use GSA (benefits):
- Centralized policy: apply Conditional Access, MFA, and session controls uniformly.
- Reduced attack surface: no need to open inbound ports or publish apps directly.
- Better security posture: policies can consider device posture, user risk, and contextual signals.
- Operational simplicity: connectors handle connectivity and scale without complex VPN appliances.
- Improved user experience: access to internal apps can be seamless and tied to single sign-on.

TL;DR: Use GSA to secure internal apps without public exposure and to replace broad VPN access with identity- and policy-based controls for better security and UX.

---

## 5. What GSA replaces or complements

GSA is not a single product replacement but a modern approach that can replace or complement several legacy patterns:

- VPNs: Replaces broad network-level VPN access for many scenarios by providing app- or resource-level connectivity.
- On-prem reverse proxies / WAFs: Complements or replaces components like WAP when used with modern cloud proxies (Entra Application Proxy and GSA connectors).
- Legacy federation-only flows: Works alongside Entra Conditional Access instead of relying on older on-prem-only controls.

GSA is part of the shift from network perimeter security to identity- and policy-driven access.

TL;DR: GSA reduces reliance on VPNs and legacy reverse proxies by enabling identity-based access; it complements Entra App Proxy and Conditional Access.

---

## 6. Create and configure remote networks (high-level)

This is a summary of the steps (refer to Microsoft Learn for details):

1. Plan network scope — identify subnets and resources that should be reachable through the remote network.
2. Deploy connectors/gateways in the remote network (on-prem VMs or VNets). These run the software that creates secure outbound tunnels to Entra.
3. Register the remote network in the Entra portal and associate connectors with it.
4. Configure DNS and routing if necessary so connectors can reach the intended resources.
5. Create policies (Conditional Access and session controls) that apply to access flows targeting the remote network resources.
6. Test connectivity and refine policies to balance security and usability.

TL;DR: Plan, deploy connectors, register the network, configure routing/DNS, apply policies, and test — connectors make outbound, secure connections to Entra.

See the Microsoft Learn module for the step-by-step UI and CLI commands: https://learn.microsoft.com/en-us/training/modules/deploy-configure-microsoft-entra-global-secure-access/6-create-remote-networks

---

## 7. Key concepts and components

- Connector / Gateway: Runs in the remote network, establishes outbound tunnels to Entra, and forwards authorized traffic.
- Remote network: The on-premises or private cloud network registered with Entra.
- Conditional Access: Policy engine that evaluates sign-ins and enforces MFA, device compliance, and other requirements.
- Session controls: Controls that apply after sign-in to monitor or restrict a session (e.g., apply app-enforced restrictions).
- Short-lived credentials / tokens: Entra issues tokens/credentials for approved sessions instead of long-lived network credentials.

TL;DR: Remember connectors, remote networks, Conditional Access, session controls, and short-lived tokens — these are the building blocks of GSA.

---

## 8. Practical scenarios and examples

- Remote worker needs access to an internal HR web app. Instead of VPN, the connector proxies the request and Entra applies MFA and device checks.
- A partner needs limited access to a specific service in a branch office; you register the branch as a remote network and apply a scoped policy.
- Migration: decommissioning VPN concentrators by creating remote networks for specific application flows.

TL;DR: Real-world uses include user access to intranet apps without VPNs, scoped partner access, and migrating away from VPN concentrators.

---

## 9. Resources and further reading

- Microsoft Learn: Deploy and configure Microsoft Entra Global Secure Access — module: Create remote networks
  - https://learn.microsoft.com/en-us/training/modules/deploy-configure-microsoft-entra-global-secure-access/6-create-remote-networks
- Microsoft Docs: Global Secure Access overview
  - https://learn.microsoft.com/en-us/entra/global-secure-access/

TL;DR: Start with the Microsoft Learn module and the official docs linked above.

---

## 10. Glossary

- GSA — Global Secure Access
- Connector — software that connects remote network to Entra control plane
- Remote network — an on-prem or private network registered for access
- Conditional Access — policy engine in Entra
- Entra Application Proxy — older/similar capability for publishing apps

TL;DR: Use this glossary for quick reference to GSA terms.

---

*Last updated: 2026 — Personal study notes for SC-300*
