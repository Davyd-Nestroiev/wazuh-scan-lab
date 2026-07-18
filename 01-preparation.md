# Preparation

## Objective 
Build a purple-team home lab to safely practice offensive (attack) and defensive (detection/response) workflows, then document the full cycle against PTES (attack side) and NIST SP 800-61 / PICERL (defense side) as a portfolio piece.

## Scope
- **Attacker:** Kali Linux VM
- **Target:** DVWA (Damn Vulnerable Web Application) running on an Ubuntu victim VM
- **Monitoring:** Wazuh SIEM, agent-based, watching the victim VM
- All systems are isolated on a single NAT virtual subnet — no exposure outside the host machine.

## Environment

| Component | Details |
|---|---|
| Host | MacBook Air (Apple Silicon, 16GB RAM) |
| Hypervisors | VMware Fusion + OrbStack |
| Wazuh server | OrbStack — Ubuntu 24.04 LTS ARM64 |
| Victim VM | Ubuntu 24.04, hostname `ubuntu-4ubrick`, IP `192.168.99.130` |
| Attacker VM | Kali Linux 2026.2, hostname `kali-attacker`, IP `192.168.99.131` |
| Networking | All VMs on NAT, same subnet (`192.168.99.0/24`), confirmed via ping |

## Setup

- SSH access confirmed to the victim VM (`ssh davyd@192.168.99.130`)
- Kali attacker VM built and confirmed able to reach the victim VM over the network
- Metasploitable2 was ruled out (x86_64-only, incompatible with Apple Silicon); DVWA was chosen instead, since it runs natively on ARM via Docker, and was deployed directly on the victim VM so Wazuh has visibility into any attack traffic
- Wazuh agent installed on the victim VM and confirmed active/connected in the Wazuh dashboard

For setup issues encountered along the way and how they were resolved, see [Troubleshooting](troubleshooting.md).

## Monitoring baseline

Before running any attacks, the Wazuh agent on the victim VM was confirmed active and connected in the dashboard, with no prior alerts generated.

## Next Step
Proceed to Reconnaissance — begin identifying live hosts and enumerating exposed services on the target.
→ [02 - Reconnaissance](02-reconnaissance.md)
