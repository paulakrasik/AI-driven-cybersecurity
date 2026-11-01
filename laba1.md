# Lab 1a: Rule-Based Detection & MITRE ATT&CK
---
## System Verification & Finding Counts:
**Initial Baseline:** Upon launching the dashboard with the clean baseline log, the initial finding count was exactly **2**.
**System State (Screenshot):** The attached dashboard screenshot was taken after running the automated simulator (Activity D). It demonstrates the system dynamically detecting new synthetic events, which increased the total finding count to **10**.
<img width="1280" height="577" alt="5997076863227989428" src="https://github.com/user-attachments/assets/6b43639f-1dbf-4a7b-b9c3-17c3b8e2fb42" />
<img width="1280" height="340" alt="5997076863227989426" src="https://github.com/user-attachments/assets/44923840-daeb-4d23-bed9-d4f7e6cac937" />
<img width="1280" height="492" alt="5997076863227989427 (1)" src="https://github.com/user-attachments/assets/82cdf274-8909-4610-b321-66aa2482330d" />
<img width="1920" height="1008" alt="497e9952-08e0-4ade-8117-d46424267ab9" src="https://github.com/user-attachments/assets/6f2474d6-f89e-4646-8929-70e817ac7abf" />




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

  ## SOC Analyst Narrative

In this lab, I acted as a SOC analyst reviewing the `security.log` file. Looking at the raw data, the facts are just lines of text. For example, I saw three lines stating "Failed password" from the IP address 172.18.0.5, and another line explicitly saying "Network scan detected" from 172.18.0.7. Based on its rules, the detector flagged these as T1110 (Brute Force) and T1046 (Network Service Discovery) activities.
However, it is important to separate these facts from interpretation. The log only proves that three login attempts failed, not that a malicious attacker was trying to brute-force the system. It could simply be a legitimate user who forgot their password. Similarly, the log just contains the text string "Network scan detected"; we don't have the actual network packets to verify if a real scan happened. As an analyst, I learned that these alerts are just indicators that require further investigation, not absolute proof of a successful cyber attack.
