# Splunk SIEM Network Reconnaissance & Scan Detection Pipeline

## Project Synopsis
This project demonstrates how a Security Operations Center (SOC) can detect reconnaissance and scan activity by simulating an attacker using Kali Linux and analyzing the resulting logs in Splunk Cloud.  
A Kali Linux system was used to perform multiple Nmap-based reconnaissance techniques against an Ubuntu target machine. The Ubuntu target generated authentication and service-related log events in `/var/log/auth.log`, which were then ingested into Splunk Cloud for analysis, correlation, and dashboard visualization.

The goal of the project was to identify how Nmap reconnaissance leaves detectable traces in Linux logs and how those traces can be transformed into actionable SOC detections inside Splunk.

---

## Technology Stack & Architecture
- **Attacker Machine:** Kali Linux
- **Target Machine:** Ubuntu Linux
- **Attack Tool:** Nmap
- **Log Source:** Ubuntu `/var/log/auth.log`
- **SIEM Platform:** Splunk Cloud
- **Detection Goal:** Identify scan-related SSH connection closures, SSH negotiation failures, repeated probing behavior, and attacker source IP activity

---

## Project Workflow

1. **Attack Simulation**  
   A Kali Linux machine was used to perform reconnaissance and scanning activity against the Ubuntu target.

2. **Log Generation**  
   The Ubuntu machine generated authentication and SSH-related log events during the scans.

3. **Log Collection**  
   Relevant Linux logs were monitored and extracted from `/var/log/auth.log`.

4. **Splunk Ingestion**  
   The collected logs were ingested into Splunk Cloud with the sourcetype **`nmap scan logs`**.

5. **Detection & Analysis**  
   Splunk SPL queries were used to identify reconnaissance events, attacker source IPs, SSH negotiation failures, and repeated connection closures.

6. **Dashboard Visualization**  
   A Splunk dashboard was built to visualize total scan events, attacker IP activity, timelines, and event distribution.

---

# Kali Linux Attack Simulation

## 1. Host Availability Check
The attacker first confirmed that the Ubuntu target was reachable by sending ICMP echo requests.

```bash
ping 192.168.64.4
```
### 2. Basic Port scan
A basic Nmap scan was performed to identify exposed services on the target system.
```bash
nmap 192.168.64.4
```
### 3. Aggressive Service & OS Enumeration
An aggressive Nmap scan was used to collect more information about the target, including service details and deeper host fingerprinting data.
```bash
nmap -A 192.168.64.4
```
### 4. Stealth / SYN Reconnaissance Scan
A stealth SYN scan was also executed to simulate more active reconnaissance behavior against the target.
```bash
nmap -sS -T4 192.168.64.4
```
### 5. High-Frequency Reconnaissance Loop
To create a larger detection footprint, repeated scans were executed in a loop from the Kali attacker machine.
```bash
for i in {1..10}; do
  nmap 192.168.64.4
done
```
# Ubuntu Target Log Telemetry

The Ubuntu target machine generated multiple SSH-related log events during the Nmap scans. These logs were observed in /var/log/auth.log and later ingested into Splunk Cloud.

The following categories of events were captured:

SSH connection closure events
SSH negotiation failures
Repeated connection attempts from the attacker IP
Protocol and host key negotiation issues caused during scan enumeration
### Connection Closure Signatures

During repeated Nmap probing, the target logged SSH pre-authentication connection closures. These occur when the scanner initiates a connection to SSH and closes it before authentication completes.
Example log event
```bash
sshd[2811]: Connection closed by 192.168.64.1 port 63455 [preauth]
```
### SSH Negotiation Failure Signatures

More aggressive or fingerprinting-oriented scans triggered SSH negotiation failures on the Ubuntu target. These events occur when the scanning tool attempts to enumerate or negotiate SSH protocol / key parameters that do not match the server configuration.
Example log event
```bash
sshd[2823]: Unable to negotiate with 192.168.64.1 port 63464: no matching host key type found. Their offer: ecdsa-sha2-nistp256
```

# Splunk SIEM Dashboards & Detection Analytics

The collected Linux logs were imported into Splunk Cloud and analyzed using SPL queries to detect scan patterns, suspicious source IP activity, and repeated authentication-related events.

Panel 1 – Reconnaissance Totals & Source Identification

This panel counts scanning-related events and identifies the attacking source IP responsible for repeated reconnaissance activity.
### SPL Query – Count reconnaissance-related events
```bash
index=main sourcetype="nmap scan logs" ("Connection closed" OR "Unable to negotiate")
| stats count
```
### SPL Query – Count events by source IP
```bash
index=main sourcetype="nmap scan logs"
| rex field=_raw "by (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count by src_ip
```
### 3. Reconnaissance Timeline

This panel visualizes when reconnaissance-related events occurred and highlights spikes caused by repeated scans.

Purpose:
Shows the time distribution of the scan activity and helps correlate attacker behavior with event bursts.

SPL Query
```bash
index=main sourcetype="nmap scan logs" ("Connection closed" OR "Unable to negotiate")
| timechart span=5m count
```
### 4. SSH Negotiation Failures

This panel displays events where the SSH daemon reported negotiation failures during scanning.

Purpose:
Helps identify aggressive enumeration attempts and SSH fingerprinting behavior.
```bash
index=main sourcetype="nmap scan logs" "Unable to negotiate"
| table _time host source sourcetype _raw
```
### 5. Connection Closure Events

This panel displays events where the attacker touched the SSH service and closed the connection before authentication.

Purpose:
Highlights repeated pre-authentication connection closures caused by scan probing.
```bash
index=main sourcetype="nmap scan logs" "Connection closed"
| table _time host source sourcetype _raw
```
### 6. Recon Event Distribution

This panel shows the distribution of reconnaissance-related event types such as Connection Closed and Negotiation Failure.

Purpose:
Allows the analyst to understand which reconnaissance behaviors are most dominant in the dataset.
```bash
index=main sourcetype="nmap scan logs"
| eval event_type=case(
    like(_raw,"%Connection closed%"), "Connection Closed",
    like(_raw,"%Unable to negotiate%"), "Negotiation Failure"
  )
| stats count by event_type
```
### Dashboard 1 – Total Reconnaissance Events & Source IP Analysis

### Dashboard 2 – Reconnaissance Timeline & SSH Negotiation Failures

### Dashboard 3 – Connection Closure Events

### Dashboard 4 – Recon Event Distribution
## Attack Execution Screenshots

### Ubuntu Log Monitoring
This screenshot shows live log activity on the Ubuntu target while reconnaissance events were being generated.

![Ubuntu Log Monitoring](live display of port scanning from kali linux(2).png)

### Kali – Host Reachability and Initial Nmap Scan
This screenshot shows the Kali attacker machine performing the target reachability check and a basic Nmap scan.

![Kali Initial Scan](port scan on ubntu ip using nmap(2).png)

### Kali – Repeated Nmap Reconnaissance Loop
This screenshot shows the repeated Nmap scanning loop used to generate a larger detection footprint in the target logs.

![Kali Recon Loop](port scan to ubuntu ip using nmap(2).png)
## Key Detection Findings

The project successfully demonstrated that Nmap reconnaissance leaves visible artifacts in Linux authentication logs, especially when scanning exposed SSH services.

- Repeated Nmap scanning generated multiple SSH connection closure events.
- Aggressive and enumeration-focused scans triggered SSH negotiation failures.
- Splunk was able to identify the attacker source IP from the collected log data.
- Time-based visualizations clearly showed the scan burst pattern.
- Event distribution charts helped distinguish connection closures from negotiation failures.
- The dashboard provided a SOC-style monitoring view of reconnaissance behavior.

## Skills Demonstrated

- Splunk Cloud dashboard creation
- Linux log analysis
- SOC detection engineering
- SPL query writing
- Nmap reconnaissance simulation
- SSH event analysis
- Security monitoring and log correlation
- Detection of suspicious source IP behavior
  
### MITRE ATT&CK Mapping

This project aligns with the following ATT&CK techniques:

T1595 – Active Scanning
T1046 – Network Service Discovery
# Outcome

This project demonstrates an end-to-end SOC detection workflow:

1. Simulate attacker reconnaissance using Nmap from Kali Linux
2. Capture resulting authentication and SSH logs on Ubuntu
3. Ingest the logs into Splunk Cloud
4. Build SPL detections for suspicious scan behavior
5. Visualize the findings in a Splunk dashboard

The result is a practical SIEM-based use case showing how network reconnaissance can be detected and investigated through Linux host telemetry.
