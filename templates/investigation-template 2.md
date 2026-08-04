### [003] - [SOC127 - SQL Injection Detected]

**Date worked:** 2026-08-04<br>
**Source:** LetsDefend SOC Simulation / Home SOC Lab<br>
**Category:** Web Attack<br>
**Severity assigned:** High<br>

---

**1. Alert Summary**
What triggered the alert? What did the SOC monitoring tool flag, and why did it warrant a look?
- The alert was triggered by a specific rule name: SOC127 - SQL Injection Detected
- SOC monitoring tool flagged: A `GET` Request
- It warrants a look at: Destination IP Address: `172.16.20.12`, Hostname: `WebServer1000`



**2. Initial Triage**
First actions taken. What did I check first, and why that, before anything else?
- Initially, I checked which `IP address/host` made the `GET` request. After a successful identification, I pinpointed the compromised endpoint and promptly isolated it from the network to prevent any potential damage or unauthorized access. Following this, I delved into the endpoint security section to gather comprehensive information about the device, which included details such as its operating system, installed applications, and any recent activities that could indicate further security concerns. By quarantining the device, I aimed to safeguard the entire network from any additional attacks and mitigate the risk of lateral movement, which could allow threats to spread to other connected systems.

**3. Investigation Timeline**

| Time | Action | Result |
|---|---|---|
| 03:35 | Reviewed Endpoint Security for the alerted IP | Destination IP `172.16.20.12` resolved to hostname `Atlanta-Server` in Endpoint Security - this did not match the hostname shown by the monitoring tool that generated the alert, indicating a possible discrepancy in asset/hostname mapping |
| 03:43 | Reviewed browser history on the source endpoint | No matching request found - traffic wasn't user-initiated via browser |
| 03:46 | Reviewed log management/proxy logs | Found additional suspicious GET requests from `118.194.247.28` to `172.16.20.12`, routed via proxy |
| 03:50 | Reviewed endpoint process activity | No suspicious Activity found. |
| 03:53 | Reviewed endpoint network connections | No suspicious Activity found. |
| 03:55 | Reviewed endpoint terminal history | No suspicious Activity found. |




**4. Investigation**
Step-by-step walkthrough of the analysis:
- Logs/tools reviewed 
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
