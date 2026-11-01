# Lab 1a: Rule-Based Detection & MITRE ATT&CK
---
## System Verification & Finding Counts:
**Initial Baseline:** Upon launching the dashboard with the clean baseline log, the initial finding count was exactly **2**.
**System State (Screenshot):** The attached dashboard screenshot was taken after running the automated simulator (Activity D). It demonstrates the system dynamically detecting new synthetic events, which increased the total finding count to **10**.

## Evidence Mapping Table
| Evidence (Raw Log) | Rule | MITRE Technique | Tactic | Uncertainty / Limitations |
| :--- | :--- | :--- | :--- | :--- |
| 3 or more "Failed password" lines from the same IP (e.g., 172.18.0.5) | Groups by Source IP; alerts on ≥3 failed logins | T1110 - Brute Force | Credential Access | The rule only counts failed attempts. It does not prove that a brute-force attack was successful or that the attacker gained access. |
| Log line containing the exact phrase: "Network scan detected" | String matching for specific text | T1046 - Network Service Discovery | Discovery | The rule relies entirely on another system's text output. It does not independently infer a scan from raw network packets. |
---
## Experiments C1–C4 Summary

* **Experiment C1 (Below the threshold):**
  I deleted one of the "Failed password" lines for IP 172.18.0.5 from the security log. I expected the T1110 alert to disappear because the rule requires at least 3 failed attempts to trigger. As expected, the alert was gone, and the dashboard only showed 1 finding (the T1046 scan).

* **Experiment C2 (Three failures from different IPs):**
  I modified the log to have three failed logins, but I used a different IP address for each line (172.18.0.5, 172.18.0.6, and 172.18.0.8). I predicted that the T1110 alert wouldn't trigger since the rule counts attempts per individual IP. This was correct; no new alert appeared, and the total findings stayed at 1.

* **Experiment C3 (Crossing the threshold with a new IP):**
  I added three fake "Failed password" records at the bottom of the file using a brand new IP (192.0.2.55). Since this hits the threshold of 3, I expected a new alert. A new High severity T1110 finding immediately showed up for that specific IP, bringing the total count to 2.

* **Experiment C4 (Adding a second network scan):**
  Finally, I added a new line that explicitly said "Network scan detected" from another new IP (192.0.2.77). I predicted the system would catch the text string and add another T1046 alert. A second Medium severity T1046 finding appeared on the dashboard, increasing the total findings to 3.

  ## Simulator Observation
  When the simulator was running, the total finding count continuously increased over time as new automated events were appended to the log. The simulator is less reproducible than controlled manual experiments because it generates random, unpredictable synthetic events (such as varied IPs and attack patterns) at set intervals, constantly changing the baseline data before exact measurements can be manually verified.
