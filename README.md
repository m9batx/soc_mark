### SOC M's Architecture - Integrated Security Operations Center Architecture system combined from set of open source tools

<img width="1091" height="524" alt="Screenshot 2026-10-18 190600" src="https://github.com/user-attachments/assets/addf64f8-afc8-428b-849d-4e111349776c" />







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

1. **The Perimeter (DMZ Arch):** Handles ingress traffic, email security, and public-facing service protection.
2. **The Endpoint (EP Agents):** Monitors internal workstations and servers for malicious activity.
3. **The Core (SOC Arch):** The central brain where logs are analyzed, threats are detected, and automated responses are triggered.

---

### 1. DMZ – Network Security

The DMZ layer provides an additional security boundary between the public Internet and the internal LAN. Public-facing services, such as web applications and email infrastructure, are placed in this segment so that potential compromises are contained before they can reach internal systems or endpoints.

| # | Component | Role |
|---|-----------|------|
| 1 | **T-Pot** | All-in-one open-source honeypot platform supporting 20+ low- and high-interaction honeypots. Used to detect, attract, and analyze malicious activity targeting exposed services. [GitHub](https://github.com/telekom-security/tpotce) |
| 2 | **SafeLine WAF** | Self-hosted, open-source Web Application Firewall that operates as a reverse proxy. Monitors, filters, and blocks malicious HTTP/HTTPS traffic (SQLi, XSS, exploit attempts, etc.). [GitHub](https://github.com/chaitin/safeline) |
| 3 | **Proxmox Mail Gateway** | Open-source email security gateway and mail proxy. Filters both inbound and outbound email traffic, inspecting and sanitizing messages before they reach internal users. [Official site](https://www.proxmox.com/en/products/proxmox-mail-gateway/overview) |
| 4 | **Fail2ban** | Open-source intrusion-prevention tool that monitors server logs and automatically bans IPs after suspicious behavior (repeated failed logins, exploit scanning, etc.). [GitHub](https://github.com/fail2ban/fail2ban) |

**Security Role of the DMZ**  
If a public website is hosted in this environment, SafeLine WAF and Fail2ban act as the primary protective layer against web-based attacks. SafeLine inspects and filters incoming HTTP/HTTPS requests, while Fail2ban detects abusive behavior and automatically blocks offending source IP addresses.

For email services, Proxmox Mail Gateway is deployed in the DMZ to filter, inspect, and validate incoming and outgoing email traffic. This ensures that potentially malicious messages, attachments, or links are identified and mitigated within the DMZ before they reach a user’s mailbox on an internal endpoint. As a result, an exploit delivered through email is contained at the perimeter rather than being exposed directly to the LAN environment.

This design follows a defense-in-depth approach: public-facing attack surfaces are isolated in the DMZ, malicious traffic is filtered before reaching internal resources, and any compromise is less likely to propagate into the internal network. T-Pot provides a multi-honeypot platform for observing attacker behavior and generating high-fidelity threat intelligence.

---

### 2. Endpoint Security (EP Agents)

Endpoint agents provide deep visibility into workstations, servers, and other assets inside the trusted network. They collect telemetry, enforce detection rules, and feed the core SOC platform.

| # | Component | Role |
|---|-----------|------|
| 5 | **Velociraptor Agent** | Lightweight endpoint agent that performs digital forensics and incident response (DFIR) collections, hunting, and live response. Collects artifacts on demand or continuously and streams them to the Velociraptor server. |
| 6 | **Wazuh Agent** | Multi-platform agent that performs log collection, File Integrity Monitoring (FIM), rootkit detection, vulnerability assessment, and active response. Sends events to the Wazuh manager. |

**Key capabilities at the endpoint layer**
- Continuous monitoring of system logs, processes, network connections, and file changes.
- On-demand forensic collections via Velociraptor (memory, registry, files, etc.).
- Local active response (e.g., blocking an IP or killing a process) coordinated by Wazuh.
- Minimal performance impact while providing high-fidelity telemetry to the SOC core.

---

### 3. Core SIEM & Analysis

This is the central processing and storage layer of SOC Mark.

| # | Component | Role |
|---|-----------|------|
| 7 | **Velociraptor Server** | Central management and collection server for all Velociraptor agents. Stores collected artifacts, runs hunts, and provides a powerful query language (VQL) for investigations. |
| 8 | **Wazuh Server Side** | Full Wazuh stack: **Manager** (rule engine & active response), **Indexer** (OpenSearch-based storage), and **Dashboard** (visualization & alerting). Acts as the primary SIEM/XDR platform. |
| 9 | **RedGray** | Event enrichment / correlation engine that sits between Wazuh and the orchestration layer. Receives processed alerts and prepares them for automated workflows. |

Wazuh receives agent data, applies detection rules, correlates events, and generates alerts. Velociraptor complements this with deep forensic capabilities that can be triggered on demand or via automation.

---

### 4. Orchestration & Intelligence

This layer turns detections into actionable, enriched, and automated responses.

| # | Component | Role |
|---|-----------|------|
| 10 | **Shuffle** | Open-source Security Orchestration, Automation and Response (SOAR) platform. Receives alerts from Wazuh/RedGray and executes playbooks (enrichment, containment, notification, ticket creation). |
| 11 | **Intel Owl** | Threat intelligence aggregation platform. Queries multiple external and internal sources (VirusTotal, GreyNoise, AbuseIPDB, MISP, OpenCTI, etc.) and returns structured enrichment data to Shuffle. |
| 12 | **IRIS** | Incident Response platform. Receives cases from Shuffle, tracks investigations, maintains timelines, and stores evidence. |
| 13 | **n8n** | Workflow automation engine used for lighter or custom integrations (especially outbound notifications). |
| 14 | **Mail** | Final notification channel. Alerts and reports are delivered to the SOC team via email. |
| 15 | **Probo** | Complementary security tooling (vulnerability / continuous testing component) integrated into the SOC visibility plane. |
| 16 | **MONOROc** | Additional monitoring / observability component feeding the overall SOC dashboard and alerting pipeline. |

**Threat Intelligence Connectors** (queried primarily via Intel Owl / Shuffle):
- VirusTotal  
- GreyNoise  
- AbuseIPDB  
- MISP  
- OpenCTI  

---

## 🔄 Data Flow

1. **Ingress / Perimeter**  
   Internet traffic → Firewall → DMZ (T-Pot, SafeLine WAF, Proxmox Mail Gateway, Fail2ban).  
   Malicious activity is either blocked, logged, or captured by honeypots.

2. **Endpoint Telemetry**  
   Velociraptor agents (5) and Wazuh agents (6) continuously collect data from endpoints and forward it to their respective servers (7 & 8).

3. **Central Detection**  
   Wazuh Manager processes logs, applies rules, and generates alerts.  
   RedGray (9) receives and prepares these alerts for orchestration.

4. **Orchestration & Enrichment**  
   Alerts flow into **Shuffle** (10).  
   Shuffle triggers playbooks that:
   - Query **Intel Owl** (11) for threat intelligence enrichment.
   - Create or update cases in **IRIS** (12).
   - Optionally use **n8n** (13) for additional automation.

5. **Notification & Response**  
   Enriched alerts and case updates are sent via n8n → Mail (14).  
   Analysts work the cases in IRIS while Shuffle can also perform automated containment actions (via Wazuh active response or Velociraptor).

6. **Supporting Visibility**  
   Probo (15) and MONOROc (16) provide additional continuous testing and monitoring feeds into the same orchestration and dashboard layer.

---

## ✨ Key Features

- **Defense-in-depth**: Clear separation between DMZ, endpoints, and SOC core.
- **Open-source first**: Almost the entire stack is FOSS and can be fully self-hosted.
- **Automated enrichment**: Every alert is automatically enriched with multiple threat-intel sources via Intel Owl + Shuffle.
- **Dual endpoint coverage**: Wazuh for continuous SIEM/XDR + Velociraptor for deep DFIR and hunting.
- **Flexible orchestration**: Shuffle playbooks + n8n for custom workflows.
- **Incident lifecycle management**: Full case management with IRIS.
- **Email & perimeter protection**: Proxmox Mail Gateway + SafeLine WAF + Fail2ban + T-Pot.

---

## 🚀 Deployment Guide (High-level)

1. Deploy the DMZ components (T-Pot, SafeLine, Proxmox Mail Gateway, Fail2ban) on dedicated hosts/VMs isolated from the internal network.
2. Deploy Wazuh Manager + Indexer + Dashboard and Velociraptor Server in the SOC zone.
3. Roll out Wazuh and Velociraptor agents to all endpoints.
4. Deploy Shuffle, Intel Owl, IRIS, and n8n.
5. Configure the connectors (VirusTotal, GreyNoise, AbuseIPDB, MISP, OpenCTI) inside Intel Owl / Shuffle.
6. Build and test the core playbooks in Shuffle (alert → enrich → case creation → notification).
7. Integrate Probo and MONOROc feeds into the same pipeline.



