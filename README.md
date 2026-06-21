# Splunk SIEM Network Reconnaissance & Scan Detection Pipeline

## 📌 Project Synopsis
This research demonstrates how a Security Operations Center (SOC) can identify passive and active scanning activities by simulating an actual adversary network reconnaissance campaign. Using a Kali Linux workstation as the threat actor vector, multiple intensive network discovery passes (Nmap) were executed against an infrastructure target endpoint. The resulting defensive system logs were ingested, parsed, and visualized inside Splunk Cloud to create an active security monitoring and threat-detection dashboard.

## 🛠️ Technology Stack & Architecture
* **Attacker Vector:** Kali Linux Framework (Nmap network discovery suite)
* **Target Endpoint:** Ubuntu Server running open infrastructure services (OpenSSH daemon)
* **Telemetry Source:** Linux Host Operating System Security Logs (`/var/log/auth.log`)
* **SIEM Platform:** Splunk Cloud Engine for log parsing, normalization, and analytics

---

## ⚔️ Kali Linux Attack Simulation
To generate realistic detection telemetry, multiple reconnaissance phases were simulated from the attacker machine:

### 1. Initial Host Discovery & Service Probe
The attacker maps the network using baseline pings and targeted stealth configuration sweeps (`-sS`) to identify active live hosts and services:

```bash
# Verify host availability
ping 192.168.64.4

# Run baseline port discovery scan
nmap 192.168.64.4
