### SOC M's Architecture - Integrated Security Operations Center Architecture system combined from set of open source tools

<img width="1130" height="575" alt="Screenshot 2026-10-16 211359" src="https://github.com/user-attachments/assets/a7611d4b-a8aa-47f7-9c04-721a3c875653" />




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
The Perimeter (DMZ Arch)
1. T-Pot 
2. Safeline WAF -  (Web Application Firewall) is primarily used as an open-source, self-hosted security shield that sits in front of web applications, web servers, and homelabs. Operating as a reverse proxy, its main purpose is to monitor, filter, and block malicious incoming HTTP/HTTPS traffic before it can exploit vulnerabilities on your backend servers

3. Proxmox Mail Gateway
4. fail2fail 




resources 
https://medium.com/@ludovic.doamba/wazuh-at-the-heart-of-a-sovereign-soc-architecture-for-public-and-critical-infrastructures-1d2808a0af61#1
https://github.com/arviiyer/threat-intel-soc 
https://github.com/Cyberfortress-Labs/Cyberfortress-Intelligent-SOC-Ecosystem
