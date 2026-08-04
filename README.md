<h1 align="center">🚨 Incident Documentation</h1>
<h3 align="center">Real-world triage practice, documented like an analyst - not a student</h3>

<p align="center">
  <img src="https://img.shields.io/badge/Platform-LetsDefend-1E1E1E?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Type-Blue_Team_Practice-blue?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Status-Ongoing-brightgreen?style=for-the-badge"/>
</p>

<p align="center">
<b>Alert → Triage → Investigation → Evidence → Verdict → Response → Lessons Learned</b>
</p>

---

## 🎯 Why This Repository Exists

Anyone can say "I completed X alerts on LetsDefend." A recruiter can't verify that, and it doesn't show *how* I think. This repo exists to prove **process** - the same triage logic, evidence-gathering, and decision-making a working SOC Analyst uses, applied to every incident I work through.

Every write-up is in my own words, based on what I actually found - not copied from a platform's answer key.

> **Note:** While most investigations here come from LetsDefend, some are real incidents from building my own SOC home lab - like PKI/certificate failures I had to root-cause myself. These aren't simulated; they're genuine troubleshooting under time pressure.

---

## 🗃️ How This Repository Is Organized

- **This README is the master index** - a live summary of everything in the repo.
- Each investigation lives in its **own file** under [`/investigations`](./investigations), named `[ID]-[short-title].md`.
- The reusable write-up structure lives in [`/templates/investigation-template.md`](./templates/investigation-template.md) - copy that file to start a new entry.

---

## 📊 Stats

*Updates will be provided as investigations are completed.*

| Metric | Count |
|---|---|
| Total investigations | 2 |
| Phishing alerts | 0 |
| Malware alerts | 1 |
| Brute force / credential attacks | 0 |
| Infrastructure / PKI incidents | 1 |
| True Positives | 1 |
| False Positives | 0 |
| Benign True Positives | 0 |

---

## 🗂️ Investigation Index

| ID | Investigation | Category | Severity | Verdict | Report |
|---|---|---|---|---|---|
| 001 | Malicious-File-Script-Download-Attempt | Malware | Medium | True Positive | [View](investigations/Malicious-File-Script-Download-Attempt.md) |
| 002 | Kibana–Elasticsearch TLS Trust Failure After IP Re-Addressing | Infrastructure / PKI | Medium | True Positive | [View](./investigations/002-kibana-elasticsearch-tls-trust-failure.md) |

---

## 🧩 Skills Demonstrated Across This Repository

As investigations accumulate, this tracks the range of SOC competencies shown-useful for a recruiter scanning for specific skills.

- [ ] Phishing email analysis (headers, links, attachments)
- [ ] Log correlation across multiple sources
- [ ] IOC identification & lookup (VirusTotal, AbuseIPDB, etc.)
- [x] Malware behavior analysis
- [ ] Brute force / credential attack detection
- [ ] Insider threat / anomalous user behavior
- [x] Incident severity classification
- [ ] Escalation decision-making
- [x] PKI / TLS certificate troubleshooting
- [x] Systematic root-cause analysis under compounding failures
- [x] Service/log diagnostics (systemd, journalctl)

---

## 🔭 Roadmap

- [ ] Add first LetsDefend phishing investigation
- [x] Add first malware analysis investigation with full MITRE ATT&CK mapping
- [ ] Build a simple script to auto-generate the stats table from investigation files
- [ ] Add a "false positive" example investigation-showing sound judgment matters as much as catching true threats
- [ ] Cross-link relevant investigations to the SOC Home Lab Build doc where the two intersect

---

<p align="center"><i>Companion repository to the SOC Home Lab Build - this repo covers hands-on incident response practice, while the lab doc covers the infrastructure being built to eventually run these investigations independently.</i></p>
