
# Lab 1 | From CTI to SOC Investigation and MITRE ATT&CK Mapping
---
## Individual Submission
* Submitted by: Paula Krasikov
---
## CTI Report 
* Report Title: Multiple Threat Actors Exploit React2Shell (CVE-2025-55182)
* Publisher: Google Threat Intelligence Group (GTIG) / Mandiant
* Publication Date: December 12, 2025
* Direct URL: https://cloud.google.com/blog/topics/threat-intelligence/threat-actors-exploit-react2shell-cve-2025-55182
---
## Attack Summary

This report examines real-world attacks exploiting CVE-2025-55182 (also known as "React2Shell"), a critical vulnerability in React Server Components used within Next.js web applications. The observed activity involves several threat groups, mainly espionage actors linked to China (such as UNC6600 and UNC6595), alongside opportunistic attackers who install cryptocurrency miners.

The attack starts when an attacker sends a malicious web request to an exposed server, allowing them to execute arbitrary commands without needing credentials. Once they gain access, the attackers launch command shells to download additional tools and malware onto the machine. To maintain access after reboots, they create persistent background services and scheduled tasks. They also establish network tunnels to keep a connection to their outside servers despite firewall restrictions. Throughout the intrusion, the attackers try to avoid detection by disguising their files under legitimate system names, altering file timestamps, and deleting their command history to make forensic analysis much harder. 
---
## Attack Diagram
<img width="1170" height="588" alt="5976726612394840095" src="https://github.com/user-attachments/assets/59c62937-51e1-466f-b0e1-ee9a33469850" />

## Attack Mapping table 

| Observed Behavior | ATT&CK Tactic | Technique / Sub-Technique (ID) | Evidence and Mapping Rationale | SOC Telemetry / Investigation |
| :--- | :--- | :--- | :--- | :--- |
| Exploitation of an unauthenticated remote flaw via crafted web traffic | **Initial Access** | **Exploit Public-Facing Application** (`T1190`) | The report identifies external HTTP requests exploiting React Server Components to execute code on exposed servers. | **Telemetry:** Web application firewall and server access logs.<br>**Investigation:** Correlate inbound web traffic anomalies with immediate outbound network activity from the web application process. |
| Ingestion and execution of remote installer scripts via command shells | **Execution** | **Command and Scripting Interpreter: Unix Shell** (`T1059.004`) | Threat actors retrieved and executed remote setup scripts through standard system command shells. | **Telemetry:** Operating system process creation events and parent-child telemetry.<br>**Investigation:** Detect web service processes unexpectedly spawning command shells that initiate file downloads. |
| Creation of background system services and scheduled recurring jobs | **Persistence** | **Create or Modify System Process: Systemd Service** (`T1543.002`) / **Scheduled Task/Job: Cron** (`T1053.003`) | Attackers configured new background service units and cron entries to ensure implants survive system restarts. | **Telemetry:** File integrity monitoring on system service paths and scheduled task tables.<br>**Investigation:** Review newly registered system background services for references to untrusted binary paths. |
| Disguising malicious binaries under legitimate system service names | **Defense Evasion** | **Masquerading: Match Legitimate Name or Location** (`T1036.005`) | Attackers placed implants inside configuration directories while naming them after legitimate remote access daemons. | **Telemetry:** Endpoint detection process execution paths and system binary verification logs.<br>**Investigation:** Compare running binary paths against authorized system package databases to flag services launching from abnormal directories. |
| Deletion of interactive command session history | **Defense Evasion** | **Indicator Removal: Clear Command History** (`T1070.003`) | Attackers erased terminal session history immediately after running setup actions to hinder forensic review. | **Telemetry:** System command auditing and shell logging streams.<br>**Investigation:** Flag shell sessions that invoke history-clearing instructions or modify session history storage. |

## SOC Investigation Notes

### Behavior 1: Web Application Exploitation & Shell Invocation
* **Tier 1 (Triage):**
  * Check EDR alert for web process spawning a command shell.
  * Verify external destination IP and isolate the server.
* **Tier 2 (Deep Investigation):**
  * Review web server logs to locate the exploit payload.
  * Extract and analyze the downloaded script to find C2 infrastructure.

### Behavior 2: System Persistence via Background Services and Tasks
* **Tier 1 (Triage):**
  * Review alerts for new system services or cron jobs against IT change tickets.
* **Tier 2 (Deep Investigation):**
  * Check the service executable hash in threat databases.
  * Investigate how the web account gained administrative permissions.

### Behavior 3: Masquerading and Session History Removal
* **Tier 1 (Triage):**
  * Flag standard process names running from abnormal paths.
* **Tier 2 (Deep Investigation):**
  * Perform memory and disk forensics to inspect file metadata and recover deleted commands.
