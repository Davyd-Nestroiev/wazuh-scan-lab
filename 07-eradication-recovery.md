# Stage 7: Eradication & Recovery

## Objective
During this stage, the blue team has to eradicate the vulnerabilities exploited in previous stages, and proceed with system and service recovery steps.

## Eradication 
Increased the DVWA application security level. This does not completely resolve the fundamental vulnerability, but adds additional steps to exploit it, an attacker would need additional tools (e.g. an intercepting proxy) to replay the session-based request, increasing the effort required to exploit the system.

## Recovery
The system was restored to an operational state with the security level set to High (the Stage 6 firewall rule remained in place throughout). However, recovery is only partial, as the SQL injection is still exploitable via the session-based ID mechanism, meaning the fundamental flaw remains post-recovery. Full recovery would require replacing the vulnerable query with a parameterised statement, as demonstrated by DVWA's "Impossible" level.

## Outcomes
- Increasing DVWA's security level to High reduced the attack surface by removing direct URL-based injection, forcing the attacker through the session-based ID mechanism instead.
- Despite this, the same payload (' OR '1'='1) still succeeded when submitted via that mechanism, confirming the underlying SQLi flaw was not fixed — only the delivery path was made harder.
= Root cause: DVWA's High-level source builds the query with unescaped string concatenation, identical to Low; no mysqli_real_escape_string() or parameterization is applied.
- Demonstrates a real-world pattern: a configuration-level control can raise the effort required to exploit a vulnerability without eliminating it, which is a meaningfully different outcome from true remediation.
- True eradication would require a code-level fix (parameterized queries/prepared statements), as used in DVWA's "Impossible" level.

## Screenshots

exploitation attempt requires additional steps
<img width="676" height="442" alt="Screenshot 2026-07-26 at 12 09 06" src="https://github.com/user-attachments/assets/92509e4d-b781-4b38-aedd-0b8eee0d04e9" />

## Next Steps 
n the next phase, Stage 8 will cover Post-Incident Activity - documenting the exploited vulnerabilities and drawing lessons for improvement.

-->[Post-Incident Activity](08-post-incident-activity.md)
