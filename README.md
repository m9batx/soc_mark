### SOC M's Architecture - Integrated Security Operations Center Architecture system combined from set of open source tools

<img width="1272" height="582" alt="Screenshot 2026-10-09 144606" src="https://github.com/user-attachments/assets/6bdde73d-f1ae-4821-8cd5-cf1b228f17c5" />



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


