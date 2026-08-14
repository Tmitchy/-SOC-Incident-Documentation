### [003] - [SOC287 - Arbitrary File Read on Checkpoint Security Gateway]

**Date worked:** 2026-08-04<br>
**Source:** LetsDefend SOC Simulation / Home SOC Lab<br>
**Category:** Web Attack<br>
**Severity assigned:** High<br>
**CVE:** [CVE-2024-24919]<br>

---

**1. Alert Summary**
What triggered the alert? What did the SOC monitoring tool flag, and why did it warrant a look?
- The alert was triggered by a specific rule name: SOC287 - Arbitrary File Read on Check Point Security Gateway
- The SIEM monitoring tool flagged: A `POST` request to an IP address that was attempting to access `aCSHELL/../../../../../../../../../../etc/passwd`
- The SIEM warrants a look at: Destination IP Address: `172.16.20.146`, Hostname: `CP-Spark-Gateway-01`, Port: `53423`



**2. Initial Triage**
First actions taken. What did I check first, and why that, before anything else?
- Initially, I checked which `IP address/host` made the `POST` request. After a successful identification, I pinpointed the compromised endpoint and promptly isolated it from the network to prevent any potential damage or unauthorized access. Following this, I delved into the endpoint security section to gather comprehensive information about the device, which included details such as its operating system, installed applications, and any recent activities that could indicate further security concerns. By quarantining the device, I aimed to safeguard the entire network from any additional attacks and mitigate the risk of lateral movement, which could allow threats to spread to other connected systems.

**3. Investigation Timeline**

| Time | Action | Result |
|---|---|---|
| 03:43 | Reviewed browser history on the destination endpoint | No matching request found on the browser history |
| 03:46 | Reviewed log management logs | Found additional suspicious POST requests from `203.160.68.12` to `172.16.20.146`, routed via proxy |
| 03:50 | Reviewed endpoint process activity | No suspicious Activity found. |
| 03:53 | Reviewed endpoint network connections | Found and confirmed the Source IP address that made the request. |
| 03:55 | Reviewed endpoint terminal history | No suspicious Activity found. |




**4. Investigation**
Step-by-step walkthrough of the analysis:
- Logs/tools reviewed:
   - Log Management Tool: To confirm the connection pattern and frequency.
   - Endpoint Security (EDR): To check what process on the host initiated the connection.
   - DNS logs: To see if the destination IP was reached via a domain lookup or a hardcoded IP.
   - VirusTotal: Checked the reputation of the destination IP and any associated domain.
- Indicators found:
   - The source IP `203.160.68.12`, port `24124`, sent a POST request to the firewall(Check Point Security Gateway) requesting the `/clients/MyCRL` remote path to access the directory where the users are stored by modifing and sending another POST request that contain the directory traversal payload `aCSHELL/../../../../../../../../../../etc/passwd`.
   - EDR failed to indicate that the connection originated from the firewall shell; instead, it provided details about the network connection, showing the destination IP address and the active processes, including their IDs.
   - No matching DNS query preceded the connections — the IP was hardcoded, not resolved via a domain.
   - VirusTotal flagged the IP as malicious (7/94 vendors), associated with China Unicom Global.

- Any pivoting done:
  - All sections were clovered.

**5. IOCs (Indicators of Compromise)**

| Type | Value | Notes |
|---|---|---|
| IP | 203.160.68.12 | The IP address making the POST request for directory traversal. |
| Port | 24124 | The port used by the source IP to initiate the connection. |
| URL | /clients/MyCRL | The web remote path the adversary used to request a directory. |



| Tactic | Technique | ID |
|---|---|---|
| Initial Access | Exploit Public-Facing Application | T1190 |
| Credential Access | OS Credential Dumping | T1003 |
| Discovery | File and Directory Discovery | T1083 |


**7. Evidence**
Screenshots, log snippets, or IOC tables that support the findings.


![](https://github.com/Tmitchy/-SOC-Incident-Documentation/blob/main/images/SIEM%20Review.png)

![](https://github.com/Tmitchy/-SOC-Incident-Documentation/blob/main/images/Log%20Management%20Review%20.png)

![](https://github.com/Tmitchy/-SOC-Incident-Documentation/blob/main/images/EDR%20Review.png)

![](https://github.com/Tmitchy/-SOC-Incident-Documentation/blob/main/images/Virustotal-1%20Review.png)


**8. Verdict**
True Positive: The web attack was successful. After analyzing the tools used to review logs and gather information, it was found that the firewall allowed the POST request, resulting in the attacker gaining access to private information.

**9. Response / Containment**
What action was taken (or would be taken in a live environment):
  - Isolated the host
  - Blocked the IP
  - Disabled the account
  - Escalate to Tier 2.

**10. Lessons Learned**
What this incident taught me — a detection gap, a new tool feature, a faster triage path, or a concept I had to look up.
