### SOC M's Architecture - Integrated Security Operations Center Architecture system combined from set of open source tools

<img width="1102" height="537" alt="Screenshot 2026-10-18 185303" src="https://github.com/user-attachments/assets/1ec7206e-56e4-4a2d-8be5-aacc806201eb" />






**SOC MARK** is a comprehensive, open-source-based Security Operations Center (SOC) architecture designed to centralize threat detection, log analysis, and incident response across hybrid environments. It integrates Endpoint Detection and Response (EDR), Network Detection, and File Integrity Monitoring (FIM) into a unified visibility plane.

By leveraging **Wazuh** as the core SIEM/XDR engine and **Shuffle** for orchestration, SOC Mark automates the correlation of security events and enriches them with global threat intelligence.

---

## 📑 Table of Contents
1. [System Architecture](#system-architecture)
2. [Component Breakdown](#component-breakdown)
    - [DMZ](#1-dmz--network-security)
    - [Endpoint Security (EP Agents)](#2-endpoint-security-ep-agents)
    - [Core SIEM & Analysis](#3-core-siem--analysis)
    - [Orchestration & Intelligence](#4-orchestration--intelligence)
3. [Data Flow](#data-flow)
4. [Key Features](#key-features)
5. [Deployment Guide](#deployment-guide)

---

## 🏗 System Architecture

SOC Mark is divided into three primary functional zones to ensure defense-in-depth:

1.  **The Perimeter (DMZ Arch):** Handles ingress traffic, email security, and public-facing service protection.
2.  **The Endpoint (EP Agents):** Monitors internal workstations and servers for malicious activity.
3.  **The Core (SOC Arch):** The central brain where logs are analyzed, threats are detected, and automated responses are triggered.

---
**The Perimeter (DMZ Arch):** 

The DMZ layer provides an additional security boundary between the public Internet and the internal LAN. Public-facing services, such as web applications and email infrastructure, are placed in this segment so that potential compromises are contained before they can reach internal systems or endpoints
 
1. T-Pot - an all-in-one open-source honeypot platform, supports 20+ low- and high-interaction honeypots, It is used to detect, attract, and analyze malicious activity targeting exposed services. 
https://github.com/telekom-security/tpotce

2. Safeline WAF -  A self-hosted, open-source Web Application Firewall that operates as a reverse proxy in front of web applications and web servers. It monitors, filters, and blocks malicious HTTP/HTTPS traffic, protecting backend services from common web attacks such as SQL injection, XSS, and exploit attempts before they reach the application.
https://github.com/chaitin/safeline

3. Proxmox Mail Gateway - An open-source email security gateway and mail proxy that filters both inbound and outbound email traffic. It is deployed in the DMZ to inspect and sanitize messages before they are delivered to internal mail users.
https://www.proxmox.com/en/products/proxmox-mail-gateway/overview

4. fail2Ban - an open-source intrusion-prevention tool that watches server logs and automatically bans IPs after suspicious behavior, such as repeated failed logins or exploit scanning
https://github.com/fail2ban/fail2ban

Security Role of the DMZ
If a public website is hosted in this environment, SafeLine WAF and Fail2ban act as the primary protective layer against web-based attacks. SafeLine inspects and filters incoming HTTP/HTTPS requests, while Fail2ban detects abusive behavior and automatically blocks offending source IP addresses.
For email services, Proxmox Mail Gateway is deployed in the DMZ to filter, inspect, and validate incoming and outgoing email traffic. This ensures that potentially malicious messages, attachments, or links are identified and mitigated within the DMZ before they reach a user’s mailbox on an internal endpoint. As a result, an exploit delivered through email is contained at the perimeter rather than being exposed directly to the LAN environment.
This design follows a defense-in-depth approach: public-facing attack surfaces are isolated in the DMZ, malicious traffic is filtered before reaching internal resources, and any compromise is less likely to propagate into the internal network. SafeLine is specifically designed as a self-hosted WAF and reverse proxy to protect web applications from attacks and exploits, while T-Pot provides a multi-honeypot platform for observing attacker behavior.




  **The Endpoint (EP Agents):**

resources 
1. https://medium.com/@ludovic.doamba/wazuh-at-the-heart-of-a-sovereign-soc-architecture-for-public-and-critical-infrastructures-1d2808a0af61#1
2. https://github.com/arviiyer/threat-intel-soc
3. https://github.com/Cyberfortress-Labs/Cyberfortress-Intelligent-SOC-Ecosystem

