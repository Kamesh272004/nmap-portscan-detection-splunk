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
2. Deep OS Fingerprinting & High-Frequency Volume Loops

An aggressive automation loop combined with deep service profiling flags (-A and -T4) is deployed from Kali Linux to test log ingestion bandwidth and trigger threshold alarms:

# Trigger aggressive service profiling and OS detection
nmap -A -T4 192.168.64.4

# Volumetric script simulation loop (rapid scanning pass)
for i in {1..10}; do nmap 192.168.64.4; done


3. Target Host System Log Telemetry

Looking directly at the active target machine, the automated loops generate a high-volume trail of negotiation and connection drops inside /var/log/auth.log:

🛡️ Defensive Analysis & Log Footprint

The automated scans created distinctive threat signatures within the host endpoint's telemetry logs.

Stealth Fingerprinting Signatures: The scanner initiated and immediately tore down connections to map open ports without authenticating, generating consecutive drop footprints:

sshd[2811]: Connection closed by 192.168.64.1 port 63455 [preauth]


Cipher/Protocol Enumeration Signatures: The scanner passed deprecated cryptographic algorithms to sweep for software version vulnerabilities, creating active negotiation failures:

sshd[2823]: Unable to negotiate with 192.168.64.1 port 63464: no matching host key type found. Their offer: ecdsa-sha2-nistp521 [preauth]


📊 Splunk SIEM Dashboards & Detection Analytics

The unstructured logs were imported into a centralized Splunk portal. Custom Search Processing Language (SPL) queries monitor the following metrics:

Panel 1: Reconnaissance Totals & Source Identification

Counts distinct scanning alerts to watch for high-volume sweeps and explicitly exposes the specific source host vector orchestrating the probe traffic (192.168.64.1).

index=main sourcetype="nmap scan logs" ("Connection closed" OR "Unable to negotiate")
| stats count


index=main sourcetype="nmap scan logs" 
| rex field=_raw "by (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count by src_ip


Panel 2: Threat Timeline & Core Authentication Failures

Visualizes the exact behavioral spike when the automated scanning scripts were running, alongside granular SSH log fields.

Panel 3: Inbound Connection Closures & Tactical Event Breakdown

Tracks stealth connection drops and uses visual distributions to map the profiling signature of the network scanning tool.
