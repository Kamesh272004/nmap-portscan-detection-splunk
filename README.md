# Splunk SIEM Network Reconnaissance & Scan Detection Pipeline

## 📌 Project Synopsis
This research shows how a Security Operations Center (SOC) may identify scanning activity by simulating an actual adversary reconnaissance campaign. 
An infrastructure target was subjected to several intense network discovery passes (Nmap) using a Kali Linux workstation as the threat actor vector. 
An active threat-monitoring interface was created by ingesting, parsing, and visualising the generated defensive system logs using Splunk Cloud.

## Technology Stack & Architecture* The Kali Linux Framework (Nmap network sweep tools) is the attack vector.
* Ubuntu Server with open infrastructure services (OpenSSH daemon) is the target endpoint.
Operating System Security Logs (`/var/log/auth.log`) are the source of the telemetry.
* SIEM Platform:** Splunk Cloud Engine for graphical dashboard metrics, log processing, and normalisation
# ⚔️ Kali Linux Attack Simulation
Several stages of reconnaissance were simulated from the attacker machine in order to produce realistic detection telemetry:
*Host Verification (ICMP Sweep):*bash ping 192.168.64.4
