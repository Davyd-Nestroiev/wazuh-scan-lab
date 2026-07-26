# Stage 8: Post-Incident Activity

## Objective
During this stage, the incident-response team has to perform post-incident activities, including documenting the whole incident lifecycle, the measures implemented in response, and the conclusions the team has learned. The team then has to suggest the best applicable recommendations for improving the company's security posture, in order to avoid and mitigate future incidents.

## Incident Summary
The attacker began by scanning the target system (4ubrick) to identify live hosts and open ports, then researched the discovered services — OpenSSH, Apache, and PHP — for known vulnerabilities. No exploitable CVEs were found in these services directly, but the DVWA web application itself was identified as vulnerable to SQL injection.

The attacker exploited this vulnerability through DVWA's SQL Injection page, using the payload ' OR '1'='1 to bypass the query logic and extract user data from the database.

The defending team detected this activity in Wazuh, confirming the malicious request had reached the log pipeline and matched a known rule. The team then contained the threat by blocking the attacker's IP address at the firewall level, adding an iptables rule to the DOCKER-USER chain to ensure it applied to the Dockerized DVWA service.

Eradication was attempted by increasing DVWA's security level from Low to High, which added a session-based mechanism for submitting the ID and removed direct URL-based injection. However, the underlying flaw was not fully resolved — the same payload still succeeded when submitted through the session mechanism, since DVWA's High-level source does not sanitise the input.

The system was recovered to an operational state with the security level set to High, but recovery was only partial due to the persisting vulnerability. Full remediation would require replacing the vulnerable query with a parameterised statement.

## Conclusiions 
- Security controls should be tested, not assumed — DVWA's High security level looked like a fix on the surface but the SQLi payload still worked once tested properly.
  
- Escaping or restricting input paths isn't the same as fixing a vulnerability - only a code-level fix (parameterized queries) actually closes it.

- Network-level containment (the firewall block) stops the attack traffic, but also stops it from reaching the application logs - so it blinds the SIEM to further attempts from that source, which is a trade-off worth knowing.
  
- Logging pipelines need to be verified end-to-end before relying on them - Wazuh initially missed the Stage 4 attack traffic, which had to be diagnosed and fixed separately.
  
- Overall, the exercise showed a full attacker-to-defender lifecycle end-to-end: recon, exploitation, detection, containment, and a (partial) fix, which is the main value of the project as a portfolio piece.

## Recommendations
- Replace string-concatenated SQL queries with parameterized statements across the application, closing the SQLi vulnerability at the code level rather than relying on input restrictions.
  
- Add server-side input validation (e.g. enforcing numeric type on the `id` field) as a second layer, not a replacement for parameterization.
  
- Set the `HttpOnly` flag on session cookies and add missing security headers (CSP, HSTS, X-Content-Type-Options) — both flagged by Nikto in Stage 3 and never addressed.
  
- Pair network-level containment with log-based monitoring on the blocking device itself (e.g. firewall logs), so blocked attempts are still visible even after the SIEM stops seeing application traffic.
  
- Verify SIEM log ingestion end-to-end before an incident, not during one — the Wazuh gap in Stage 4 could have cost visibility in a real scenario.

