<h1 align="center">🚨 SOC Incident Investigation Log</h1>
<h3 align="center">Real-world triage practice, documented like an analyst — not a student</h3>

<p align="center">
  <img src="https://img.shields.io/badge/Platform-LetsDefend-1E1E1E?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Type-Blue_Team_Practice-blue?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Status-Ongoing-brightgreen?style=for-the-badge"/>
</p>

---

## 🎯 Why I document incidents this way

Anyone can say "I completed X alerts on LetsDefend." A recruiter can't verify that, and it doesn't show *how* I think. This log exists to prove **process**, not just completion — the same triage logic, evidence-gathering, and decision-making a working SOC Analyst uses, applied to each practice incident I work through.

Every entry below follows the same structure a real SOC ticket would: alert received → investigation → evidence → decision → lessons learned. The goal is a portfolio a hiring manager can skim and immediately see I can *reason through* an incident, not just click through a platform.

---

## 🧭 How to read this log

Each incident gets its own entry using the template below. Entries are added as I complete them on LetsDefend; nothing is backfilled or embellished after the fact.

---

## 📁 Incident Entry Template

> Copy this block for each new incident.

```markdown
### Incident #[number] — [Short Descriptive Title]

**Date worked:** YYYY-MM-DD
**Source:** LetsDefend SOC Simulation
**Alert type:** e.g. Phishing / Malware / Brute Force / Suspicious Login / Data Exfiltration
**Severity assigned:** Low / Medium / High / Critical

---

**1. Alert Summary**
What triggered the alert? What did the SOC monitoring tool flag, and why did it warrant a look?

**2. Initial Triage**
First actions taken. What did I check first, and why that, before anything else?

**3. Investigation**
Step-by-step walkthrough of the analysis:
- Logs/tools reviewed (e.g. firewall logs, endpoint detection, email headers)
- Indicators found (IPs, hashes, domains, usernames)
- Any pivoting done (e.g. checking a suspicious IP across other log sources)

**4. Evidence**
Screenshots, log snippets, or IOC tables that support the findings.

**5. Verdict**
True Positive / False Positive / Benign True Positive — and the reasoning behind that call.

**6. Response / Containment**
What action was taken (or would be taken in a live environment) — isolate host, block IP, disable account, escalate to Tier 2, etc.

**7. Lessons Learned**
What this incident taught me — a detection gap, a new tool feature, a faster triage path, or a concept I had to look up.
```

---

## 🗂️ Incident Log

> Entries populate here as each investigation is completed on LetsDefend.

| # | Title | Alert Type | Verdict | Link |
|---|---|---|---|---|
| 001 | *(pending first write-up)* | — | — | — |

---

## 🧩 Skills Demonstrated Across This Log

As entries accumulate, this section tracks the range of SOC competencies shown across the full log, useful for a recruiter scanning for specific skills.

- [ ] Phishing email analysis (headers, links, attachments)
- [ ] Log correlation across multiple sources
- [ ] IOC identification & lookup (VirusTotal, AbuseIPDB, etc.)
- [ ] Malware behavior analysis
- [ ] Brute force / credential attack detection
- [ ] Insider threat / anomalous user behavior
- [ ] Incident severity classification
- [ ] Escalation decision-making

---

## 📌 Notes on Format

- Each incident is written **after** completing it on LetsDefend, in my own words, not copy-pasted from the platform's answer key.
- Sensitive lab data (if any) is sanitized before posting.
- Screenshots are cropped to relevant panels only, to keep entries scannable rather than a wall of images.

---

<p align="center"><i>Companion document to the SOC Home Lab Build — this log covers hands-on incident response practice, while the lab doc covers the infrastructure being built to eventually run these investigations independently.</i></p>
