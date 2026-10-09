### SOC M's Architecture - Integrated Security Operations Center Architecture system combined from set of open source tools

<img width="1148" height="611" alt="Screenshot 2026-10-09 101742" src="https://github.com/user-attachments/assets/98677527-26f7-4b7d-bd95-173895ca653b" />


**SOC MARK** is a comprehensive, open-source-based Security Operations Center (SOC) architecture designed to centralize threat detection, log analysis, and incident response across hybrid environments. It integrates Endpoint Detection and Response (EDR), Network Detection, and File Integrity Monitoring (FIM) into a unified visibility plane.

By leveraging **Wazuh** as the core SIEM/XDR engine and **Shuffle** for orchestration, SOC Mark automates the correlation of security events and enriches them with global threat intelligence.

---

## 📑 Table of Contents
1. [System Architecture](#system-architecture)
2. [Component Breakdown](#component-breakdown)
    - [DMZ & Network Security](#1-dmz--network-security)
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


