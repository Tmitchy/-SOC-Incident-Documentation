### [003] — [SOC127 - SQL Injection Detected]

**Date worked:** 2026-08-04<br>
**Source:** LetsDefend SOC Simulation / Home SOC Lab<br>
**Category:** Web Attack<br>
**Severity assigned:** High<br>

---

**1. Alert Summary**
What triggered the alert? What did the SOC monitoring tool flag, and why did it warrant a look?
- The alert was triggered by a specific rule: SOC127 - SQL Injection Detected
- SOC monitoring tool flag: Request URL: `GET /?douj=3034%20AND%201%3D1%20UNION%20ALL%20SELECT%201%2CNULL%2C%27%3Cscript%3Ealert%28%22XSS%22%29%3C%2Fscript%3E%27%2Ctable_name%20FROM%20information_schema.tables%20WHERE%202%3E1--%2F%2A%2A%2F%3B%20EXEC%20xp_cmdshell%28%27cat%20..%2F..%2F..%2Fetc%2Fpasswd%27%29%23 HTTP/1.1 200 865`
- It warrants a look at: Source Address: `118.194.247.28`, Hostname: `Atlanta-Server`



**2. Initial Triage**
First actions taken. What did I check first, and why that, before anything else?
- Initially, I checked which `IP adress/host` made the `GET` request. After a successful identification, I pinpointed the compromised endpoint and promptly isolated it from the network to prevent any potential damage or unauthorized access. Following this, I delved into the endpoint security section to gather comprehensive information about the device, which included details such as its operating system, installed applications, and any recent activities that could indicate further security concerns. By quarantining the device, I aimed to safeguard the entire network from any additional attacks and mitigate the risk of lateral movement, which could allow threats to spread to other connected systems.

**3. Investigation Timeline**

| Time | Action | Result |
|---|---|---|
| HH:MM | What was checked/done | What it showed |

**4. Investigation**
Step-by-step walkthrough of the analysis:
- Logs/tools reviewed (e.g. firewall logs, endpoint detection, email headers)
- Indicators found
- Any pivoting done (e.g. checking a suspicious IP across other log sources)

**5. IOCs (Indicators of Compromise)**

| Type | Value | Notes |
|---|---|---|
| IP / Hash / Domain / Username | — | — |

*If no traditional IOCs apply (e.g. infrastructure incidents), state that explicitly rather than leaving this blank.*

**6. MITRE ATT&CK Mapping**

| Tactic | Technique | ID |
|---|---|---|
| — | — | — |

*If the incident isn't adversarial (e.g. a self-inflicted config error), note that plainly instead of forcing a mapping.*

**7. Evidence**
Screenshots, log snippets, or IOC tables that support the findings.

**8. Verdict**
True Positive / False Positive / Benign True Positive — and the reasoning behind that call.

**9. Response / Containment**
What action was taken (or would be taken in a live environment) — isolate host, block IP, disable account, escalate to Tier 2, etc.

**10. Lessons Learned**
What this incident taught me — a detection gap, a new tool feature, a faster triage path, or a concept I had to look up.
