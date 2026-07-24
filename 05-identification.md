# Stage 5: Identification

## Objectives 
During this stage, the blue team must identify the suspicious traffic using the Wazuh EDR tool and proceed with response according to the incident response framework.

## Traffic Captured 
Traffic that been captured using Wazuh dashboard event logs

### Reconnaissance Logs

| Rule ID | Level | Description | MITRE ATT&CK | Hit Count |
|---|---|---|---|---|
| 31101 | 5 | Web server 400 error code | — | 7,294 |
| 31151 | 10 | Multiple web server 400 error codes from same source IP | T1595.002 – Vulnerability Scanning (Reconnaissance) | 469 |
| 31168 | 15 | Shellshock attack detected | — | 4 |

### SQL Injection Logs

| Timestamp | Source IP | Rule ID | Level | Description | MITRE ATT&CK | Matched URL |
|---|---|---|---|---|---|---|
| Jul 24, 2026 @ 13:56:17.507 | 192.168.99.131 | 31103 | 7 | SQL injection attempt. | T1190 – Exploit Public-Facing Application (Initial Access) | `/index.php?option=com_contenthistory&view=history&list[ordering]=&item_id=75&type_id=1&list[select]=(se...` |
| Jul 24, 2026 @ 13:08:22.318 | 192.168.99.1 | 31164 | 6 | SQL injection attempt. | T1190 – Exploit Public-Facing Application (Initial Access); T1055 – Process Injection (Defense Evasion, Privilege Escalation) | `/vulnerabilities/sqli/?id=1' OR '1'='1&Submit=Submit` |

## Traffic Analysis Outcome
Analysis of the Wazuh manager logs confirmed detection of both reconnaissance and exploitation activity against the DVWA target on 4ubrick.

- **Reconnaissance was correctly identified.** Rule 31151 (level 10) aggregated repeated 400 errors from the same source IP within a short window and was accurately mapped to MITRE T1595.002 (Vulnerability Scanning), matching the Nikto scan behaviour. This confirms Wazuh's correlation rules can distinguish automated scanning from normal traffic without manual tuning.

- **SQL injection attempts were detected, contradicting an earlier assumption.** It was initially assumed the default ruleset had no SQLi-specific detection, based only on the generic 400-error rule (31101) surfacing during triage. Targeted searching (`data.url:*sqli*`) surfaced two dedicated rules — 31103 (level 7) and 31164 (level 6) — both tagged to MITRE T1190 (Exploit Public-Facing Application), confirming the exploitation attempt against `/vulnerabilities/sqli/` was captured end-to-end.

- **One MITRE mapping appears inaccurate.** Rule 31164 was also tagged with T1055 (Process Injection), a technique associated with in-memory code injection rather than web-based SQL injection. This is likely a generic tag applied to the broader `sqlinjection` rule group rather than a payload-specific match, and should not be read as evidence of process-level compromise.

- **Four Shellshock alerts (rule 31168, level 15) are assessed as false positives.** No Shellshock exploitation was performed, and Apache 2.4.54 was confirmed patched against related CVEs during Stage 3. These are consistent with Nikto's automated probing for the vulnerability rather than a genuine finding, and highlight the importance of validating high-severity alerts against known system state before escalating them.

## Screenshots

### Wazuh manager captured reconnaissance logs
<img width="830" height="645" alt="Screenshot 2026-07-24 at 14 30 56" src="https://github.com/user-attachments/assets/7ef2ae37-1981-45b7-a9ab-90c10774f148" />


### Wazuh manager captured SQL injection logs
<img width="826" height="348" alt="Screenshot 2026-07-24 at 14 02 11" src="https://github.com/user-attachments/assets/98e7bbaa-4dbc-41d0-bd25-f09d00bbcb84" />



## Next Steps 
The suspicious traffic been identified, and inspeted. Next step is containment, isolating the affected system and preventing further exploitation of the identified vulnerabilities. 

--> [06 - Containment](06-containment.md)
