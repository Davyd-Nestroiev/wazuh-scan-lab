# Setup Log

## Environment
- Host: MacBook Air (Apple Silicon)
- Hypervisor: VMware Fusion + OrbStack
- Wazuh server: OrbStack (Ubuntu 24.04 LTS ARM64)
- Victim VM: Ubuntu 24.04, hostname `ubuntu-4ubrick`, IP 192.168.99.130
- Attacker VM: Kali Linux 2026.2, hostname `kali-attacker`, IP 192.168.99.131
- Networking: all VMs on NAT, same subnet (192.168.99.0/24), confirmed via ping

## 2026-07-02 — SSH access to victim VM
- Installed and enabled `openssh-server` on `ubuntu-4ubrick`
- Confirmed active via `systemctl status ssh`
- Connected from Mac: `ssh davyd@192.168.99.130`

## 2026-07-02 — Kali attacker VM build
- Downloaded Kali 2026.2 ARM64 installer ISO from official source, verified SHA256 checksum
- Installed via VMware Fusion (Debian 13.x ARM64 guest OS type)
- Confirmed networking: Kali can ping Ubuntu victim, 0% packet loss

## 2026-07-02 — Initial recon (Nmap)
- Command: `nmap -sV 192.168.99.130`
- Result: only port 22 open (OpenSSH 9.6p1). Victim is a modern, patched install — no exploitable surface as-is.

## 2026-07-02 — Pivot to DVWA
- Metasploitable2 confirmed x86_64-only, incompatible with Apple Silicon without emulation
- Switched to DVWA — runs natively on ARM via Docker

## 2026-07-05 — Moved DVWA onto the victim VM
- Reason: DVWA was running on the Mac host, so Wazuh had no visibility into attack traffic. Moved it onto `ubuntu-4ubrick` so attack, log, and detection all happen on the monitored machine.
- Installed Docker on Ubuntu; original `vulnerables/web-dvwa` image failed (`exec format error`) due to arm64/amd64 mismatch
- Found `cytopia/dvwa` as an arm64-compatible alternative — requires Docker Compose
- Installed the modern `docker compose` plugin (not legacy `docker-compose`) via Docker's official apt repo
- Hit a `docker.socket` conflict mid-install (new packages overwrote existing systemd units) — resolved with `systemctl daemon-reload` + `reset-failed` on both units
- Cloned `cytopia/dvwa`, ran `make start` → failed: Makefile hardcodes the legacy `docker-compose` (hyphenated) binary name, which doesn't exist since we installed the plugin version

## 2026-07-14 — Login loop bug: hardcoded cookie domain

- Symptom: DVWA login kept looping back to the login page with no visible error, in both browser and via curl
- Diagnostic path: verified DB connectivity (OK) → checked CSRF token logic (`checkToken()`) → traced to session cookies not persisting → found `Set-Cookie` header included the port (`domain=192.168.99.130:8000`), which is invalid per cookie spec — browsers/curl silently reject it
- Root cause: `dvwaPage.inc.php` line 55 sets the session cookie domain directly from `$_SERVER['HTTP_HOST']`, which includes the port on a non-standard port setup (`:8000`)
- Fix: patched line 55 to strip the port — `'domain' => explode(':', $_SERVER['HTTP_HOST'])[0]`
- Verified: confirmed via curl round-trip that `PHPSESSID` now persists across requests, and DB now shows `users` and `guestbook` tables created successfully
- **Caveat:** this fix lives inside the running container's filesystem, not the source image — it will be wiped if the container is rebuilt or `docker compose down` is run. Worth re-applying or turning into a proper patch/volume mount later.
- Confirmed login (admin/password) successful — full DVWA dashboard loaded


## Next steps
- Set DVWA security level to Low, run first attack (SQL injection) from Kali
- Check Wazuh dashboard for the resulting alert
- Screenshot the full chain: attack → exploit → Wazuh alert → log detail
- Write up findings as a short LinkedIn post / portfolio entry

## Concepts learned
- NAT vs Bridged networking
- SHA256 checksum verification
- `nmap -sV` — service/version detection
