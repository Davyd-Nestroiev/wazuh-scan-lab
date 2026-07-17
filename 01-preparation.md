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
