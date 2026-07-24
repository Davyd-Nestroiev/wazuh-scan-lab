# Stage 5: Identification

## Objectives 
During this stage, the blue team must identify the suspicious traffic using the Wazuh EDR tool and proceed with response according to the incident response framework.

## Traffic Captured 

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

## Screenshots

### Wazuh manager captured SQL injection logs
<img width="826" height="348" alt="Screenshot 2026-07-24 at 14 02 11" src="https://github.com/user-attachments/assets/98e7bbaa-4dbc-41d0-bd25-f09d00bbcb84" />



## Next Steps 
