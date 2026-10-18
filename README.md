### SOC M's Architecture - Integrated Security Operations Center Architecture system combined from set of open source tools

<img width="1091" height="524" alt="Screenshot 2026-10-18 190600" src="https://github.com/user-attachments/assets/addf64f8-afc8-428b-849d-4e111349776c" />







**SOC MARK** is a comprehensive, open-source-based Security Operations Center (SOC) architecture designed to centralize threat detection, log analysis, and incident response across hybrid environments. It integrates Endpoint Detection and Response (EDR), Network Detection, and File Integrity Monitoring (FIM) into a unified visibility plane.

By leveraging **Wazuh** as the core SIEM/XDR engine and **Shuffle** for orchestration, SOC Mark automates the correlation of security events and enriches them with global threat intelligence.

## Table of Contents

1. [System Architecture](#system-architecture)
2. [Component Breakdown](#component-breakdown)
   - [1. DMZ – Network Security](#1-dmz--network-security)
   - [2. Endpoint Security (EP Agents)](#2-endpoint-security-ep-agents)
   - [3. Core SIEM & Analysis](#3-core-siem--analysis)
   - [4. Orchestration & Intelligence](#4-orchestration--intelligence)
   - [5. ISMS / Governance Layer](#5-isms--governance-layer)
3. [Data Flow](#data-flow)
4. [Key Features](#key-features)
5. [ISO 27001 Alignment](#iso-27001-alignment)
6. [Deployment Guide](#deployment-guide)

---

## System Architecture

SOC MARK is divided into four primary functional zones to ensure defense-in-depth and full ISO 27001 coverage:

1. **The Perimeter (DMZ Arch)** – Handles ingress traffic, email security, and public-facing service protection.
2. **The Endpoint (EP Agents)** – Monitors internal workstations and servers for malicious activity.
3. **The Core (SOC Arch)** – Central detection, correlation, enrichment, orchestration, and incident response.
4. **ISMS / Governance Layer** – Formal Information Security Management System (policies, Statement of Applicability, risk register, assets, training, and audit evidence).

---

## Component Breakdown

### 1. DMZ – Network Security

The DMZ provides an additional security boundary between the public Internet and the internal network. Public-facing services are placed here so that potential compromises are contained before they can reach internal systems.

| #  | Component                | Role |
|----|--------------------------|------|
| 1  | **T-Pot**                | All-in-one open-source honeypot platform supporting 20+ low- and high-interaction honeypots. Used to detect, attract, and analyze malicious activity targeting exposed services. [GitHub](https://github.com/telekom-security/tpotce) |
| 2  | **SafeLine WAF**         | Self-hosted open-source Web Application Firewall operating as a reverse proxy. Filters and blocks malicious HTTP/HTTPS traffic (SQLi, XSS, exploit attempts, etc.). [GitHub](https://github.com/chaitin/safeline) |
| 3  | **Proxmox Mail Gateway** | Open-source email security gateway that filters inbound and outbound email traffic before messages reach internal users. [Official site](https://www.proxmox.com/en/products/proxmox-mail-gateway/overview) |
| 4  | **Fail2ban**             | Open-source intrusion-prevention tool that monitors logs and automatically bans IP addresses after suspicious behavior (failed logins, scanning, etc.). [GitHub](https://github.com/fail2ban/fail2ban) |

**Security role of the DMZ**  
SafeLine WAF and Fail2ban protect web applications. Proxmox Mail Gateway sanitizes email. T-Pot captures attacker behavior and generates high-fidelity threat intelligence. This design isolates public attack surfaces and reduces the chance of lateral movement into the internal network.

---

### 2. Endpoint Security (EP Agents)

| #  | Component               | Role |
|----|-------------------------|------|
| 5  | **Velociraptor Agent**  | Lightweight DFIR agent for on-demand and continuous forensic collections, hunting, and live response. Streams artifacts to the Velociraptor server. |
| 6  | **Wazuh Agent**         | Multi-platform agent providing log collection, File Integrity Monitoring (FIM), rootkit detection, vulnerability assessment, and active response. |

**Key capabilities**
- Continuous monitoring of logs, processes, network connections, and file changes
- On-demand forensic collections (memory, disk, registry, etc.)
- Local active response coordinated by Wazuh
- High-fidelity telemetry with minimal performance impact

---

### 3. Core SIEM & Analysis

| #  | Component                | Role |
|----|--------------------------|------|
| 7  | **Velociraptor Server**  | Central management and collection server. Stores artifacts, runs hunts, and provides a powerful query language (VQL). |
| 8  | **Wazuh Server Side**    | Full stack: Manager (rule engine & active response), Indexer (OpenSearch), and Dashboard. Primary SIEM/XDR platform. |
| 9  | **RedGray**              | Event enrichment and correlation engine sitting between Wazuh and the orchestration layer. |

---

### 4. Orchestration & Intelligence

| #  | Component     | Role |
|----|---------------|------|
| 10 | **Shuffle**   | Open-source SOAR platform. Receives alerts and executes playbooks (enrichment, containment, notification, case creation). |
| 11 | **Intel Owl** | Threat intelligence aggregation platform. Queries VirusTotal, GreyNoise, AbuseIPDB, MISP, OpenCTI, and other sources. |
| 12 | **IRIS**      | Incident Response platform for case management, timelines, and evidence storage. |
| 13 | **n8n**       | Workflow automation engine used for lighter or custom integrations (especially notifications). |
| 14 | **Mail**      | Final notification channel for the SOC team. |
| 15 | **Probo**     | Complementary continuous testing / vulnerability component. |

**Threat Intelligence Connectors** (via Intel Owl / Shuffle):
- VirusTotal
- GreyNoise
- AbuseIPDB
- MISP
- OpenCTI

---

### 5. ISMS / Governance Layer

This layer sits **outside** the operational SOC and provides the formal Information Security Management System required by ISO 27001.

| Component          | Role |
|--------------------|------|
| **ISMS Builder**   | Self-hosted ISMS platform covering Policy management, Statement of Applicability (SoA), Risk Register, Security Goals, Asset inventory, BCM, GDPR, Training, Legal, Audit Findings, and reports. Supports ISO 27001:2022, NIS2, GDPR, and BSI IT-Grundschutz. [Official repository](https://github.com/coolstartnow/isms-builder) |
| **MONARC**         | Open-source risk assessment platform based on ISO/IEC 27005. Used for detailed structured risk analyses that feed into the ISMS Builder risk register. [Official site](https://www.monarc.lu) |

> **Important notes**
> - ISMS Builder is under active development and maintained primarily by one person. Core modules are functional, but it is not yet a fully finished enterprise product.
> - It does **not** automatically ingest live alerts or telemetry from Wazuh, Shuffle, IRIS, or other SOC tools. Data can be entered manually, imported via JSON, or pushed through its REST API (custom integration required).
> - Always use only the official repository: https://github.com/coolstartnow/isms-builder  
>   Fake repositories distributing malware have been reported.

---

## Data Flow

1. **Ingress / Perimeter**  
   Internet traffic → Firewall → DMZ (T-Pot, SafeLine WAF, Proxmox Mail Gateway, Fail2ban). Malicious activity is blocked, logged, or captured by honeypots.

2. **Endpoint Telemetry**  
   Velociraptor agents and Wazuh agents collect data and forward it to their respective servers.

3. **Central Detection**  
   Wazuh Manager processes logs, applies rules, and generates alerts. RedGray prepares events for orchestration.

4. **Orchestration & Enrichment**  
   Alerts flow into Shuffle. Playbooks query Intel Owl for threat intelligence, create/update cases in IRIS, and optionally trigger n8n workflows.

5. **Notification & Response**  
   Enriched alerts and case updates are sent via n8n → Mail. Analysts work cases in IRIS. Automated containment can be performed via Wazuh Active Response or Velociraptor.

6. **Supporting Visibility**  
   Probo provides continuous testing and vulnerability feeds.

7. **ISMS / Governance**  
   High-level findings, residual risks, and control effectiveness data can be transferred manually or via custom API into ISMS Builder. Detailed risk analyses from MONARC are imported into the ISMS Builder risk register.

---

## Key Features

- Defense-in-depth with clear separation of DMZ, endpoints, SOC core, and ISMS governance
- Fully open-source and self-hostable stack
- Automated alert enrichment with multiple threat intelligence sources
- Dual endpoint coverage (Wazuh continuous monitoring + Velociraptor deep DFIR)
- Flexible orchestration (Shuffle + n8n)
- Complete incident lifecycle management with IRIS
- Strong perimeter protection (WAF, mail gateway, honeypots, Fail2ban)
- Formal ISMS capabilities (policies, SoA, risk register, assets, training, audit evidence) via ISMS Builder + MONARC

---

## ISO 27001 Alignment

SOC MARK supports an Information Security Management System in accordance with **ISO/IEC 27001:2022**.

### Organizational Controls

| Control | Title | How SOC MARK supports it |
|---------|-------|--------------------------|
| 5.7     | Threat intelligence | Intel Owl + Shuffle continuously enrich alerts with multiple TI sources |
| 5.24    | Incident management planning | IRIS + Shuffle playbooks |
| 5.25    | Assessment of security events | Wazuh rules + RedGray + Shuffle |
| 5.26    | Response to incidents | Automated containment + IRIS case management |
| 5.27    | Learning from incidents | Full case history and timelines in IRIS |
| 5.28    | Collection of evidence | Velociraptor forensic collections + IRIS evidence storage |

### Technological Controls

| Control | Title | How SOC MARK supports it |
|---------|-------|--------------------------|
| 8.7     | Protection against malware | Wazuh agents, T-Pot, SafeLine WAF, Proxmox Mail Gateway |
| 8.8     | Management of technical vulnerabilities | Wazuh vulnerability detection + Probo |
| 8.15    | Logging | Comprehensive log collection via Wazuh agents |
| 8.16    | Monitoring activities | Continuous monitoring by Wazuh, Velociraptor, SafeLine, Fail2ban, T-Pot |
| 8.20–8.22 | Network security & segregation | Explicit DMZ architecture |
| 8.23    | Web filtering | SafeLine WAF |

### Management System Support

ISMS Builder + MONARC provide the formal management-system elements (Clauses 4–10): policies, Statement of Applicability, risk register, security objectives, asset management, training, governance, and audit evidence.

> **Note**  
> SOC MARK provides a strong technical and governance foundation. Formal risk assessment, documented policies, management commitment, and internal audits are still required for ISO 27001 certification.

---

## Deployment Guide (High-level)

1. Deploy DMZ components (T-Pot, SafeLine, Proxmox Mail Gateway, Fail2ban) on isolated hosts/VMs.
2. Deploy Wazuh Manager + Indexer + Dashboard and Velociraptor Server in the SOC zone.
3. Roll out Wazuh and Velociraptor agents to all endpoints.
4. Deploy Shuffle, Intel Owl, IRIS, and n8n.
5. Configure threat intelligence connectors inside Intel Owl / Shuffle.
6. Build and test core Shuffle playbooks (alert → enrich → case → notify).
7. Deploy MONARC for detailed ISO 27005 risk analyses.
8. Deploy ISMS Builder (official repository only) as the central ISMS platform.
9. Optionally create custom integrations (API or n8n/Shuffle) to push high-level findings into ISMS Builder.
10. Integrate Probo feeds into the pipeline.


resources 
1. https://medium.com/@ludovic.doamba/wazuh-at-the-heart-of-a-sovereign-soc-architecture-for-public-and-critical-infrastructures-1d2808a0af61#1
2. https://github.com/arviiyer/threat-intel-soc
3. https://github.com/Cyberfortress-Labs/Cyberfortress-Intelligent-SOC-Ecosystem
