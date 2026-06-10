# SOC Home Lab: Open-Source SIEM Deployment & Threat Detection

A hands-on SOC home lab built with Wazuh, Suricata, and DVWA to simulate 
real-world attack scenarios and practice security monitoring, log analysis, 
and incident detection.

---

## 🧪 Lab Environment

| Machine | Role | IP |
|---|---|---|
| Ubuntu Server | Wazuh SIEM + Suricata IDS | 192.168.100.131 |
| Windows 10 | Monitored Endpoint + DVWA | 192.168.100.128 |
| Kali Linux | Attacker Machine | 192.168.100.130 |

---

## 🛠️ Tools Used

- **Wazuh** — SIEM platform for log collection, alert generation, and detection
- **Suricata** — Network IDS for traffic analysis and intrusion detection
- **Sysmon** — Windows endpoint telemetry
- **DVWA** — Vulnerable web application for attack simulation
- **XAMPP** — Local web server stack on Windows
- **Hydra** — RDP brute force simulation
- **Nmap** — Network reconnaissance
- **curl** — Web attack simulation

---

## 🎯 Attack Scenarios

| # | Scenario | Tool | Detection |
|---|---|---|---|
| 1 | Network Reconnaissance | Nmap | Suricata + Wazuh rule 100030 |
| 2 | RDP Brute Force | Hydra | Wazuh rules 100021, 100025 |
| 3 | SQL Injection | curl | Wazuh rule 100040 |
| 4 | Cross-Site Scripting (XSS) | curl | Wazuh rule 100042 |

---

## 🔍 Key Skills Demonstrated

- SIEM deployment and configuration
- Custom Wazuh and Suricata rule writing
- Web application attack detection via Apache log analysis
- Endpoint monitoring with Sysmon and Windows Event Logs
- MITRE ATT&CK technique mapping
- SOC alert analysis and incident interpretation

---

## 📄 Report

Full project report with architecture, build steps, attack simulations, 
alert analysis, and SOC interpretation is included in this repository.
