# wazuh-scan-lab
Home purple-team project, simulating an attack performed inside a virtual environment, which is then detected and analyzed using the Wazuh SIEM, then contained and remediated following an incident-response process.

## Overview
This lab simulates a small internal network under attack. Three systems are used: one acts as the vulnerable target, running an intentionally exploitable web application; a second acts as the attacker, probing the network to find live hosts and exposed services before identifying and exploiting that vulnerable application to gain unauthorized access to data; and a third runs a Wazuh SIEM, which receives logs from an agent installed on the target and catches the attack as it happens, generating the evidence needed to detect, contain, and respond to it.

## Architecture 
- Kali Linux (attacker) -- VMware Fusion
- Ubuntu Victim VM "4ubrick" (target) -- VMware Fusion, running DVWA via Docker as the attack surface
- Wazuh SIEM manager -- OrbStack (Ubuntu 24.04 ARM64), with a Wazuh agent on 4ubrick reporting back to it
- All three systems sit on the same NAT subnet

## Project Documentation Structure 

This project is documented across 8 stages, following PTES (attacker side) 
and NIST SP 800-61 / PICERL (defender side):

1. Preparation — lab build + baseline
2. Reconnaissance / Information Gathering — nmap scans, service enumeration
3. Vulnerability Analysis — CVE research per exposed service
4. Exploitation — SQLi against DVWA
5. Identification — Wazuh detection of the attack
6. Containment
7. Eradication & Recovery
8. Post-Incident Activity — lessons learned / report

## Status 
🚧 In progress -- Stage 04, Exploitation

## Documentation
1. [Preparation](01-preparation.md)
2. [Reconnaissance](02-reconnaissance.md)
3. [Vulnerability-Analysis](03-vulnerability-analysis.md)
4. [Exploitation](04-exploitation.md)
