# Stage 6: Containment

## Objective 
During this stage, the defending team have to isolate the affected system (4ubrick) to prevent further exploitation of the SQL injection vulnerability identified in Stage 5, while preserving evidence for later analysis.

## Containment Steps

Configured an `iptables` rule on the `INPUT` chain of 4ubrick to drop traffic from the identified attacker IP (192.168.99.131)
- Verified with `curl` and `nmap` — found port 80 was still reachable, despite all other ports correctly showing as filtered
- Diagnosed the gap using `iptables -L --line-numbers`: traffic destined for the DVWA Docker container is handled by the `FORWARD` chain (routed to the container's internal IP), not `INPUT`, so the original rule never applied to it
- Added a corrected rule to the `DOCKER-USER` chain, which Docker evaluates before its own forwarding rules
- Re-verified with `curl` and `nmap`: all ports (including 80) now show filtered/no-response, confirming the attacker IP is fully blocked


## Outcomes

Docker manages its own forwarding rules via the `FORWARD` chain, which bypasses `INPUT` entirely for container traffic — a rule placed only in `INPUT` will not block access to Dockerized services
- `DOCKER-USER` is the correct insertion point for custom rules that need to take precedence over Docker's own chains
- Wazuh logged 4 hits from the attacker IP before the fix (leaked nmap probes triggering rule 31101), and zero afterward — confirming the block works, but also revealing that network-level containment blinds the SIEM to further attempts from that source, since blocked traffic never reaches the application layer log

## Screenshots 

### Diagnosing and complete firewall configuration of containment rules.
<img width="723" height="801" alt="Screenshot 2026-07-24 at 16 07 05" src="https://github.com/user-attachments/assets/7836a9b4-8db8-4168-8083-0d77c006bcfe" />


### Attacker scan attempt fails after firewall configuration
<img width="671" height="251" alt="Screenshot 2026-07-24 at 15 57 51" src="https://github.com/user-attachments/assets/13dcd2f1-85f2-4601-b2bb-e69750b46826" />

### Wazuh manager stops receiving traffic logs from attackers IP address 
<img width="836" height="619" alt="Screenshot 2026-07-24 at 16 00 17" src="https://github.com/user-attachments/assets/4b80e37a-4d61-449b-a2ac-7b1928e263a9" />


## Next Steps
Next phase 7 of the project covers Eradication & Recovery process. during this stage, defending team will remove the vulnerability, and restore the affected systems 

--> [Eradication & Recovery](07-eradication-recovery.md)
