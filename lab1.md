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
