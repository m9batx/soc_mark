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
5. [ISO 27001 Alignment](#iso-27001-alignment)
6. [Deployment Guide](#deployment-guide)

---

## 🏗 System Architecture

SOC Mark is divided into three primary functional zones to ensure defense-in-depth:

1. **The Perimeter (DMZ Arch):** Handles ingress traffic, email security, and public-facing service protection.
2. **The Endpoint (EP Agents):** Monitors internal workstations and servers for malicious activity.
3. **The Core (SOC Arch):** The central brain where logs are analyzed, threats are detected, and automated responses are triggered.

---

## 📦 Component Breakdown

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
| 16 | **MONARC** | Open-source risk assessment and risk management platform (based on ISO/IEC 27005). Used to perform structured information security risk analyses, maintain reusable risk models, and support ISO 27001 compliance activities. [Official site](https://www.monarc.lu) |

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

6. **Supporting Visibility & Risk Management**  
   Probo (15) provides continuous testing / vulnerability-related feeds.  
   **MONARC** (16) is used for formal information security risk assessments and risk treatment tracking, feeding risk context into the overall SOC and ISMS processes.

---

## ✨ Key Features

- **Defense-in-depth**: Clear separation between DMZ, endpoints, and SOC core.
- **Open-source first**: Almost the entire stack is FOSS and can be fully self-hosted.
- **Automated enrichment**: Every alert is automatically enriched with multiple threat-intel sources via Intel Owl + Shuffle.
- **Dual endpoint coverage**: Wazuh for continuous SIEM/XDR + Velociraptor for deep DFIR and hunting.
- **Flexible orchestration**: Shuffle playbooks + n8n for custom workflows.
- **Incident lifecycle management**: Full case management with IRIS.
- **Email & perimeter protection**: Proxmox Mail Gateway + SafeLine WAF + Fail2ban + T-Pot.
- **Risk Assessment Integration**: MONARC enables structured, ISO 27005-aligned risk analyses that complement the operational detection and response capabilities of the SOC.

---

## ISO 27001 Alignment

SOC Mark is designed to support an Information Security Management System (ISMS) in accordance with **ISO/IEC 27001:2022**. The architecture directly contributes to multiple Annex A controls by providing continuous monitoring, detection, enrichment, incident handling, and automated response capabilities.

### 1. Organizational Controls

| Control | Title | How SOC Mark Supports It |
|---------|-------|--------------------------|
| **5.7** | Threat intelligence | Intel Owl + Shuffle continuously query multiple threat intelligence sources (VirusTotal, GreyNoise, AbuseIPDB, MISP, OpenCTI). Alerts are automatically enriched with external and internal TI. |
| **5.24** | Information security incident management planning and preparation | IRIS provides structured case management, playbooks, timelines, and evidence storage. Shuffle playbooks formalize response procedures. |
| **5.25** | Assessment and decision on information security events | Wazuh rules + RedGray + Shuffle evaluate events, apply enrichment, and decide whether an event becomes an incident. |
| **5.26** | Response to information security incidents | Automated containment via Wazuh Active Response and Velociraptor, case creation in IRIS, and notification via n8n/mail. |
| **5.27** | Learning from information security incidents | IRIS retains full case history, timelines, and artifacts, enabling post-incident reviews and continuous improvement. |
| **5.28** | Collection of evidence | Velociraptor performs forensic collections (memory, disk, registry, etc.). IRIS stores and tracks evidence. |

### 2. Technological Controls

| Control | Title | How SOC Mark Supports It |
|---------|-------|--------------------------|
| **8.7** | Protection against malware | Wazuh agents provide malware detection, rootkit detection, and FIM. T-Pot captures malware samples. SafeLine WAF and Proxmox Mail Gateway block delivery vectors. |
| **8.8** | Management of technical vulnerabilities | Wazuh vulnerability detection module + Probo feed vulnerability data into the same visibility and alerting pipeline. |
| **8.15** | Logging | Wazuh agents collect comprehensive system, application, and security logs. All events are centralized in the Wazuh Indexer. |
| **8.16** | Monitoring activities | Continuous monitoring is performed by Wazuh (SIEM/XDR), Velociraptor (endpoint telemetry), SafeLine WAF, Fail2ban, and T-Pot. Shuffle orchestrates monitoring outcomes. |
| **8.20** | Networks security | Network segmentation (DMZ vs internal), SafeLine WAF, Fail2ban, and Proxmox Mail Gateway protect network boundaries and services. |
| **8.21** | Security of network services | SafeLine (reverse proxy/WAF) and Proxmox Mail Gateway secure public-facing services. |
| **8.22** | Segregation of networks | Explicit DMZ architecture isolates public-facing systems (T-Pot, SafeLine, Proxmox Mail Gateway, Fail2ban) from the internal network and SOC core. |
| **8.23** | Web filtering | SafeLine WAF acts as a protective reverse proxy filtering malicious HTTP/HTTPS traffic. |

### 3. Supporting Controls (Indirect but Strong Contribution)

| Control | Title | Contribution |
|---------|-------|--------------|
| **5.1** | Policies for information security | SOC Mark operationalizes detection, response, and logging policies. |
| **8.1** | User endpoint devices | Dual agent model (Wazuh + Velociraptor) provides visibility and control over endpoints. |
| **8.2** | Privileged access rights | Active Response and forensic collections can be restricted and audited. |
| **8.9** | Configuration management | File Integrity Monitoring (FIM) in Wazuh detects unauthorized configuration changes. |
| **8.10** | Information deletion | Forensic and log retention policies can be enforced via Wazuh and IRIS. |
| **8.12** | Data leakage prevention | Monitoring of unusual data transfers and process behavior via Wazuh and Velociraptor. |

### Summary of Alignment Strengths

- **Continuous monitoring & logging** (8.15, 8.16) → Fully covered by Wazuh + Velociraptor + perimeter tools.
- **Incident management lifecycle** (5.24–5.28) → Covered end-to-end by Shuffle + IRIS + n8n + automated response.
- **Threat intelligence** (5.7) → Native integration via Intel Owl and external connectors.
- **Network segregation & perimeter protection** (8.20–8.22) → Explicit DMZ design with WAF, mail gateway, honeypots, and Fail2ban.
- **Malware & vulnerability protection** (8.7, 8.8) → Multiple overlapping controls (agents, WAF, honeypots, vulnerability scanning).
- **Risk assessment** → MONARC provides structured, ISO 27005-aligned risk analysis supporting the overall ISMS.

> **Note:** SOC Mark does **not** replace the need for formal ISMS documentation, risk assessment, policies, or management commitment required by ISO 27001. However, it provides a strong technical foundation that directly implements and evidences many of the key Annex A controls related to detection, monitoring, and incident response.

---

## 🚀 Deployment Guide (High-level)

1. Deploy the DMZ components (T-Pot, SafeLine, Proxmox Mail Gateway, Fail2ban) on dedicated hosts/VMs isolated from the internal network.
2. Deploy Wazuh Manager + Indexer + Dashboard and Velociraptor Server in the SOC zone.
3. Roll out Wazuh and Velociraptor agents to all endpoints.
4. Deploy Shuffle, Intel Owl, IRIS, and n8n.
5. Configure the connectors (VirusTotal, GreyNoise, AbuseIPDB, MISP, OpenCTI) inside Intel Owl / Shuffle.
6. Build and test the core playbooks in Shuffle (alert → enrich → case creation → notification).
7. Deploy **MONARC** for formal risk assessment and risk treatment tracking (strongly recommended for ISO 27001 alignment).
8. Integrate Probo feeds into the same pipeline.



resources 
1. https://medium.com/@ludovic.doamba/wazuh-at-the-heart-of-a-sovereign-soc-architecture-for-public-and-critical-infrastructures-1d2808a0af61#1
2. https://github.com/arviiyer/threat-intel-soc
3. https://github.com/Cyberfortress-Labs/Cyberfortress-Intelligent-SOC-Ecosystem
