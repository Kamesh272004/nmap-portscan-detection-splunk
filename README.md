# Splunk SIEM Network Reconnaissance & Scan Detection Pipeline

## Project Synopsis
This project demonstrates how a Security Operations Center (SOC) can detect network reconnaissance activity by simulating attacker behavior and analyzing the resulting system logs in Splunk Cloud. A Kali Linux machine was used to perform multiple Nmap-based scanning techniques against an Ubuntu target server. The generated Linux authentication and service logs were then collected, analyzed, and visualized in Splunk to create a SOC-style detection dashboard for suspicious scanning activity.

---

## Technology Stack & Architecture
- **Attacker Machine:** Kali Linux  
- **Target Endpoint:** Ubuntu Server  
- **Attack Tool:** Nmap  
- **Telemetry Source:** Linux authentication and system logs (`/var/log/auth.log`)  
- **SIEM Platform:** Splunk Cloud  
- **Detection Focus:** Network reconnaissance, repeated scan behavior, suspicious SSH connection attempts, and aggressive service enumeration

---

## Project Workflow
1. **Attack Simulation**  
   Reconnaissance and scanning activity were generated from a Kali Linux machine using Nmap against the Ubuntu target.

2. **Log Generation**  
   The Ubuntu target produced authentication and service-related logs during scan activity, especially when probes interacted with exposed services such as SSH.

3. **Log Collection**  
   Relevant Linux log files were collected from the target system and prepared for SIEM ingestion.

4. **Splunk Ingestion**  
   The logs were ingested into Splunk Cloud for parsing, searching, and correlation.

5. **Detection Logic**  
   SPL queries were used to identify repeated connection attempts, scan patterns, and suspicious source activity.

6. **Visualization**  
   A Splunk dashboard was created to monitor scan events, attacker IP behavior, and detection indicators in a SOC-style view.

---

## Kali Linux Attack Simulation

### 1. Initial Host Discovery & Service Probe
The attacker first verifies target availability and performs a baseline scan to identify open services.

```bash
# Verify host availability
ping 192.168.64.4

# Baseline service discovery
nmap 192.168.64.4
