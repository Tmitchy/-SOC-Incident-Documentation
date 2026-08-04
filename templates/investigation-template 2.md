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
| 03:35 | Investigated the Endpoint Security | I found the **destination IP address:** `172.16.20.12` but it linked to a different **Hostname:** `Atlanta-Server`. |
| 03:43 | Investigated the Browser History | No such request that triggered the alert was found. |
| 03:46 | Investigated the Log Management | Detected additional suspicious `GET` requests from IP address `118.194.247.28`, targeting destination IP address `172.16.20.12` via proxy. |
| 03:50 | Investigated the Endpoint process ID | No suspicious Activty found. |
| 03:53 | Investigated the Endpoint Network Action | No suspicious Activty found. |
| 03:55 | Investigated the Endpoint Terminal History | No suspicious Activty found. |




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
