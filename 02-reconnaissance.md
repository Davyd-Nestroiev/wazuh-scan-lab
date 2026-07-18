# Objective

Attacker system identifies live hosts and exposed services on the target network before assessing them for vulnerabilities. This stage answers "what's out there and what's running," without yet judging whether any of it is exploitable — that comes in Vulnerability Analysis.

## Commands Run

### 1. Host Discovery

nmap -sn 192.168.99.0/24: Swept the lab subnet to confirm live hosts — used to verify 4ubrick's IP and Kali's own IP before targeting.

### 2. Service Version Scan 

nmap -sV 192.168.99.130: Ran against the confirmed target (4ubrick) to enumerate open ports and identify running service versions.

### 3. Web Enumeration

whatweb -a 3 http://192.168.99.130:  Ran a web enumeration scan at aggression level 3 against DVWA — confirmed the application, tech stack, and session cookie behavior.

### 4. Web Vulnerability 

nikto -h http://192.168.99.130 — scanned for common misconfigs, outdated software, and missing security headers

## Findings

### 1. Host Discovery

Confirmed 4ubrick (192.168.99.130) is live on the subnet, giving a concrete target to focus all further scanning on.

### 2. Service Version Scan 

Identified port 80 (Apache 2.4.54) as the active attack surface; SSH on port 22 was ruled out as a vector since it's patched against known CVEs.

### 3. Web Enumeration 

Confirmed DVWA is running with security level set to "medium" and a PHP 8.1.16 backend — tells the attacker exactly what application and difficulty they're up against, and that the backend version is worth checking for exploit compatibility.

### 4. Web Vulnerability

Confirmed /login.php as the login entry point (independent verification); flagged both PHP 8.1.16 and Apache 2.4.54 as outdated versions, worth checking for exploits; found the DVWA security cookie is missing the HttpOnly flag, meaning it's readable/modifiable via client-side script.

## Screenshots

1. Host Discovery
<img width="473" height="289" alt="image" src="https://github.com/user-attachments/assets/ee45657e-5301-43bf-994e-eee580238355" />

2. Service Version
<img width="672" height="222" alt="image" src="https://github.com/user-attachments/assets/31f30dc3-0cc2-40dc-aff2-79c69207509f" />

 3. Web Enumeration 
<img width="1431" height="113" alt="image" src="https://github.com/user-attachments/assets/8074aecb-33c6-423c-8006-18afcdd83443" />

4. Web Vulnerability
<img width="779" height="589" alt="image" src="https://github.com/user-attachments/assets/d94c76fa-5bbc-4c60-be11-0f7c393d1004" />


## Next Steps 
Proceed to Vulnerability Analysis — cross-reference identified service versions (OpenSSH 9.6p1, Apache 2.4.54, PHP 8.1.16) against known CVEs to determine exploitability ahead of the Exploitation stage.
