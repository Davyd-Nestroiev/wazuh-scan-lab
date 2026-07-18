# Objective

Attacker system identifies live hosts and exposed services on the target network before assessing them for vulnerabilities. This stage answers "what's out there and what's running," without yet judging whether any of it is exploitable — that comes in Vulnerability Analysis.

## Commands Run

### 1. Host Discovery

nmap -sn 192.168.99.0/24: Swept the lab subnet to confirm live hosts — used to verify 4ubrick's IP and Kali's own IP before targeting.

### 2. Service Version Scan 

nmap -sV 192.168.99.130: Ran against the confirmed target (4ubrick) to enumerate open ports and identify running service versions.

## Findings
 
## Scressnshots

## Next Steps 
