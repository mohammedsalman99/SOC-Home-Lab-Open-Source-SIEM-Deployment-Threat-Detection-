# SOC Home Lab: Open-Source SIEM Deployment & Threat Detection

![Security](https://img.shields.io/badge/Security-Blue%20Team-blue)
![SIEM](https://img.shields.io/badge/SIEM-Wazuh-red)
![IDS](https://img.shields.io/badge/IDS-Suricata-orange)
![Status](https://img.shields.io/badge/Status-Completed-green)

A fully functional SOC (Security Operations Center) home lab built from 
scratch to simulate real-world attack scenarios and practice defensive 
security skills. This project covers the full detection lifecycle — from 
log generation and forwarding, to alert creation and incident analysis — 
using open-source tools running on an isolated virtual network.

This lab was built as part of my cybersecurity learning journey to move 
beyond theoretical knowledge and gain hands-on experience with SIEM 
operations, network intrusion detection, endpoint monitoring, and web 
application security.

---

## 📌 Project Objectives

- Deploy and configure a fully functional open-source SIEM environment
- Monitor endpoint activity using Windows Event Logs and Sysmon telemetry
- Detect network-level attacks using a Network Intrusion Detection System
- Extend visibility to the web application layer using Apache log forwarding
- Simulate realistic attack scenarios from an attacker machine
- Write custom detection rules
- Practice SOC analyst thinking 

---

## 🧪 Lab Architecture

All three virtual machines run on VMware with an isolated Host-Only network 
for internal attack simulation and a NAT adapter for internet access.

| Machine | OS | Role | IP Address |
|---|---|---|---|
| Wazuh Server | Ubuntu Server | SIEM + IDS | 192.168.100.131 |
| Windows 10 | Windows 10 | Endpoint + DVWA | 192.168.100.128 |
| Kali Linux | Kali Linux | Attacker Machine | 192.168.100.130 |

## 🌐 Network Architecture

```
+--------------------------------------------------+
|           Host-Only Network 192.168.100.0/24     |
|                                                  |
|  +--------------+        +--------------+        |
|  |  Kali Linux  |        | Windows 10   |        |
|  |  (Attacker)  | -----> | (Endpoint +  |        |
|  | 192.168.100  |        |    DVWA)     |        |
|  |    .130      |        | 192.168.100  |        |
|  +--------------+        |    .128      |        |
|         |                       |                |
|         |                Wazuh Agent             |
|         |                Logs Forwarded          |
|         |                       |                |
|         +----------+------------+                |
|                    |                             |
|         +----------v------------+                |
|         |    Ubuntu Server      |                |
|         |  Wazuh SIEM +         |                |
|         |  Suricata IDS         |                |
|         |  192.168.100.131      |                |
|         +-----------------------+                |
+--------------------------------------------------+

Traffic Flow:
Kali Linux ──(attack traffic)──► Windows 10 DVWA
Windows 10 ──(Wazuh Agent logs)──► Ubuntu Wazuh Server
Kali Linux ──(network traffic)──► Suricata IDS on Ubuntu
```

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| Wazuh Manager | Central SIEM — log collection, rule processing, alert generation |
| Wazuh Indexer | Data storage and search backend |
| Wazuh Dashboard | Web-based alert visualization and investigation |
| Wazuh Agent | Installed on Windows 10 to forward logs to the manager |
| Suricata | Network IDS — monitors traffic and detects malicious patterns |
| Sysmon | Windows endpoint telemetry — process creation, network connections |
| DVWA | Intentionally vulnerable web application for attack simulation |
| XAMPP | Local Apache and MySQL stack hosting DVWA on Windows 10 |
| Hydra | RDP brute force attack simulation |
| Nmap | Network reconnaissance and port scanning |
| curl | Web application attack simulation from Kali |

---

## 🏗️ Build Steps

1. **Create Virtual Machines** — Ubuntu Server, Windows 10, Kali Linux on VMware
2. **Configure Network** — Host-Only adapter for internal traffic, NAT for internet
3. **Install and Configure Wazuh** — Manager, Indexer, and Dashboard on Ubuntu
4. **Install Sysmon and Wazuh Agent** — Endpoint telemetry on Windows 10
5. **Install and Configure Suricata IDS** — Network monitoring on Ubuntu server
6. **Deploy DVWA on Windows 10** — XAMPP + DVWA + Apache log forwarding to Wazuh
7. **Prepare Kali Linux** — Verify attack tools availability
8. **Validate Pipeline** — Confirm logs, alerts, and detections are working end to end

---

## 🎯 Attack Scenarios

### 4.1 — Network Reconnaissance via Nmap
- **Tool:** Nmap
- **Technique:** T1046 — Network Service Discovery
- **Detection:** Custom Suricata rule (sid:9000001) + Wazuh rule 100030
- **Detection Chain:** Nmap scan → Suricata alert → eve.json → Wazuh fires → Dashboard

### 4.2 — RDP Brute Force Attack
- **Tool:** Hydra
- **Technique:** T1110.001 — Brute Force: Password Guessing
- **Detection:** Wazuh rules 100021 (failed login), 100025 (brute force pattern)
- **Detection Chain:** Hydra attack → Windows Event ID 4625 → Wazuh agent → Alert

### 4.3 — SQL Injection via DVWA
- **Tool:** curl
- **Technique:** T1190 — Exploit Public-Facing Application
- **Detection:** Custom Wazuh rule 100040 detecting UNION SELECT patterns
- **Detection Chain:** curl request → Apache access.log → Wazuh agent → Rule 100040 fires

### 4.4 — Cross-Site Scripting (XSS) via DVWA
- **Tool:** curl
- **Technique:** T1059.007 — JavaScript
- **Detection:** Custom Wazuh rule 100042 detecting script injection patterns
- **Detection Chain:** curl request → Apache access.log → Wazuh agent → Rule 100042 fires

---

## 🔍 Custom Detection Rules

### Suricata — Nmap Detection
### Wazuh — SQL Injection Detection
```xml
<rule id="100040" level="10">
  <if_sid>31100,31101</if_sid>
  <url>union+select|union%20select|1=1|'or'|order+by|group+by</url>
  <description>DVWA SQL Injection attempt detected</description>
  <mitre><id>T1190</id></mitre>
</rule>
```

### Wazuh — XSS Detection
```xml
<rule id="100042" level="10">
  <if_sid>31100,31101</if_sid>
  <url>%3Cscript%3E|onerror=|onload=|alert%281%29|javascript:</url>
  <description>DVWA XSS attempt detected</description>
  <mitre><id>T1059.007</id></mitre>
</rule>
```

### Wazuh — RDP Brute Force Detection
```xml
<rule id="100025" level="14" frequency="5" timeframe="60">
  <if_matched_sid>100021</if_matched_sid>
  <same_source_ip />
  <description>RDP Brute Force Attack Detected</description>
  <mitre><id>T1110.001</id></mitre>
</rule>
```

---

## 📊 MITRE ATT&CK Coverage

| Technique ID | Technique Name | Scenario |
|---|---|---|
| T1046 | Network Service Discovery | Nmap Reconnaissance |
| T1110.001 | Brute Force: Password Guessing | RDP Brute Force |
| T1190 | Exploit Public-Facing Application | SQL Injection |
| T1059.007 | JavaScript | XSS Attack |

---

## 🔑 Key Skills Demonstrated

- Open-source SIEM deployment and configuration (Wazuh)
- Network intrusion detection system setup (Suricata)
- Custom detection rule writing for both Wazuh and Suricata
- Windows endpoint monitoring with Sysmon and Event Logs
- Web application attack detection via Apache log forwarding
- Attack simulation using real penetration testing tools
- MITRE ATT&CK technique mapping and threat classification
- SOC analyst thinking — alert triage, investigation, and response

---

## 📄 Project Report

A full detailed report is included in this repository covering:
- Complete lab architecture and design decisions
- Step-by-step build documentation with screenshots
- All attack scenarios with detection chains and alert evidence
- SOC interpretation and recommended response actions for each scenario
- Conclusion and lessons learned

---

## 👤 Author

**Mohammed Salman**
Cybersecurity enthusiast focused on blue team operations, 
SOC analysis, and defensive security.

[![GitHub](https://img.shields.io/badge/GitHub-mohammedsalman99-black)](https://github.com/mohammedsalman99)
