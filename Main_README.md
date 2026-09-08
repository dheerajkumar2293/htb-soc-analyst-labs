# Hack the Box Academy SOC Analyst Job Role Path Portfolio

Complete hands-on documentation of the Hack The Box SOC Analyst certification path. Demonstrating practical cybersecurity skills across incident response, SIEM operations, forensic investigation, threat hunting, and enterprise tool mastery.

**Target Role:** Entry-Level SOC Analyst (Blue Team)  
**Status:** 🟢 Active Learning (5/15 modules complete - 33.3%)  
**Last Updated:** August 2026

---

## 📊 Progress Dashboard

```
Overall Progress: ██████████████████░░░░░░░░░░░░░░░░ 33.3% (5/15 modules)

Modules Completed:     ✅ 5
Modules In Progress:   🔄 0  
Modules Remaining:     ⏳ 10
Estimated Completion:  Early September 2026 (~15 days)

Recent Completions:
✅ Module 1: Incident Handling Process (Aug 2026)
✅ Module 2: Security Monitoring & SIEM Fundamentals (Aug 2026)
✅ Module 3: Windows Event Logs & Finding Evil (Aug 2026)
✅ Module 4: Introduction to Threat Hunting & Hunting with Elastic (Aug 2026)
✅ Module 5: Understanding Log Sources & Investigating with Splunk (Aug 2026) ← NEW!
```

---

## 📚 ALL 15 MODULES STATUS

### ✅ COMPLETED (5/15)

#### **Module 1: Incident Handling Process** ✅
- **Date:** August 2026 | **Time:** ~8 hours | **Rating:** ⭐⭐⭐⭐⭐
- **Key Skills:** NIST 6-stage framework, Cyber Kill Chain, evidence handling, escalation
- **Case Study:** Insight Nexus 47-day breach investigation

---

#### **Module 2: Security Monitoring & SIEM Fundamentals** ✅
- **Date:** August 2026 | **Time:** ~5 hours | **Points:** 20 Cubes | **Rating:** ⭐⭐⭐⭐⭐
- **Key Skills:** Elastic Stack pipeline, SIEM architecture, alert triage, MITRE ATT&CK
- **Tools:** Beats, Logstash, Elasticsearch, Kibana

---

#### **Module 3: Windows Event Logs & Finding Evil** ✅
- **Date:** August 2026 | **Time:** ~5 hours | **Points:** 20 Cubes | **Rating:** ⭐⭐⭐⭐⭐
- **Key Skills:** Windows forensics, Sysmon, Event IDs, Get-WinEvent queries, Timeline analysis
- **Attack Examples:** DLL hijacking, credential dumping (Mimikatz), PowerShell injection

---

#### **Module 4: Introduction to Threat Hunting & Hunting with Elastic** ✅
- **Date:** August 2026 | **Time:** ~5 hours | **Rating:** ⭐⭐⭐⭐⭐
- **Key Skills:** Hypothesis-driven hunting, 7-step workflow, threat intelligence, IOC hunting
- **Frameworks:** Pyramid of Pain, Diamond Model, MITRE ATT&CK for hunting
- **Application:** Dwell time reduction, proactive threat discovery, Emotet case study

---

#### **Module 5: Understanding Log Sources & Investigating with Splunk** ✅ **NEW!**
- **Date:** August 2026 | **Time:** ~8 hours (longest so far) | **Points:** 20 Cubes | **Rating:** ⭐⭐⭐⭐⭐
- **Difficulty:** Medium (Tier 2)
- **Key Skills:**
  - SPL (Splunk Processing Language) query writing
  - Log source architecture and pipelines
  - Field extraction and normalization
  - Data aggregation and statistical analysis
  - Time-based analysis and anomaly detection
- **Real Investigation:**
  - **Challenge:** Find account with highest Kerberos authentication requests
  - **Solution:** Used EventCode 4768 (TGT requests) query with stats/sort
  - **Result:** Identified `waldo` account with 12 requests (anomalously high)
  - **Extended:** Analyzed EventCode 4769 (service tickets) showing 38 total requests for system account
- **SPL Commands Mastered:**
  - `search`, `stats`, `sort`, `fields`, `where`, `dedup`, `top`, `rare`
  - `timechart`, `table`, `rename`
  - Pipe operator (|), field aggregation, comparison operators
- **Career Impact:**
  - Splunk = enterprise standard SIEM (Fortune 500 adoption)
  - Learning Splunk = massive hiring advantage
  - Immediate job relevance for enterprise SOCs

---

### 🔄 IN PROGRESS (0/15)

**Ready for Module 6: Windows Attacks & Defense**

---

### ⏳ UPCOMING (10 modules)

#### **Module 6: Windows Attacks & Defense** (Coming Soon)
Advanced Windows attack patterns and detection strategies

#### **Modules 7-15: Specialized SOC Skills**
- Network traffic analysis (Modules 7-8)
- IDS/IPS systems (Module 9)
- Malware analysis (Module 10)
- JavaScript deobfuscation (Module 11)
- YARA & Sigma detection rules (Module 12)
- Digital forensics (Module 13)
- Advanced Splunk detection (Module 14)
- Incident reporting & standards (Module 15)

---

## 🎯 CUMULATIVE TECHNICAL SKILLS

### Reactive & Proactive Operations
- ✅ **Incident Response:** Complete 6-stage NIST framework
- ✅ **SIEM Detection:** Both Elastic (open-source) and Splunk (enterprise)
- ✅ **Windows Forensics:** Event logs, Sysmon, timeline analysis
- ✅ **Threat Hunting:** Hypothesis-driven, intelligence-based searching
- ✅ **Log Analysis:** Multiple platforms (Elastic, Splunk, Windows)

### Query Languages & Tools
- ✅ **KQL (Kibana Query Language):** Elastic SIEM queries
- ✅ **SPL (Splunk Processing Language):** Enterprise SIEM queries
- ✅ **PowerShell:** Get-WinEvent log analysis
- ✅ **Data Aggregation:** Stats, sorting, field-based analysis
- ✅ **Time-Series Analysis:** Temporal anomaly detection

### Forensic & Investigation
- ✅ **Timeline Building:** Correlating events across sources
- ✅ **Evidence Preservation:** Chain of custody, forensic procedures
- ✅ **Anomaly Detection:** Baseline vs. abnormal behavior
- ✅ **Root Cause Analysis:** From alert to conclusion
- ✅ **Log Source Correlation:** Multi-source investigation

### Threat Intelligence & Frameworks
- ✅ **NIST Incident Response:** 6-stage model
- ✅ **MITRE ATT&CK:** Tactics, techniques, procedures
- ✅ **Cyber Kill Chain:** Attack lifecycle understanding
- ✅ **Pyramid of Pain:** Indicator effectiveness ranking
- ✅ **Diamond Model:** Intrusion analysis framework
- ✅ **Threat Hunting Methodology:** 7-step process

---

## 📈 CAREER READINESS ASSESSMENT

```
After Module 1 (Incident Response):
- 40% ready for SOC analyst role

After Module 2 (SIEM):
- 60% ready (can detect and respond)

After Module 3 (Windows Forensics):
- 75% ready (can investigate)

After Module 4 (Threat Hunting):
- 80% ready (can hunt proactively)

After Module 5 (Splunk):
- 85% ready (can use enterprise SIEM) ← CURRENT

Target After All 15:
- 95%+ ready for entry-level SOC analyst positions
```

**What You Can Do Now:**
- ✅ Respond to incidents professionally
- ✅ Detect threats with SIEM tools
- ✅ Investigate Windows systems
- ✅ Hunt proactively for threats
- ✅ Write queries in multiple SIEM platforms
- ✅ Correlate data for attack timelines
- ✅ Analyze Kerberos authentication patterns
- ✅ Answer sophisticated technical interview questions

---

## 💼 PORTFOLIO POSITIONING

### What Recruiters See

**GitHub Profile:** HTB Academy SOC Analyst Portfolio
- ✅ 33.3% Complete (5/15 modules)
- ✅ Multiple SIEM platforms mastered (Elastic, Splunk)
- ✅ Real investigations documented
- ✅ Professional technical writing
- ✅ Consistent learning progression

### Why This Impresses

1. **Shows Commitment:** Not just talking about cybersecurity—actively training
2. **Demonstrates Depth:** Real investigations, not theoretical
3. **Relevant Skills:** Splunk, incident response, threat hunting—what SOCs need
4. **Communication:** Clear documentation = can explain findings to non-technical stakeholders
5. **Job-Ready:** Already knows key concepts, SIEM platforms, investigation methodology

---

## 📂 Repository Organization

```
htb-soc-analyst-labs/
├── README.md (this file)
├── modules/
│   ├── 01-incident-handling/
│   │   ├── README.md (complete documentation)
│   │   └── screenshots/
│   ├── 02-siem-fundamentals/
│   │   ├── README.md
│   │   ├── siem-queries.md
│   │   └── screenshots/
│   ├── 03-windows-event-logs/
│   │   ├── README.md
│   │   ├── event-id-reference.md
│   │   ├── powershell-queries.md
│   │   └── screenshots/
│   ├── 04-threat-hunting-elastic/
│   │   ├── README.md
│   │   ├── hunting-queries.md
│   │   └── screenshots/
│   ├── 05-splunk-siem/
│   │   ├── README.md
│   │   ├── spl-queries.md
│   │   ├── kerberos-investigation.md
│   │   └── screenshots/
│   └── [Modules 6-15] (coming soon)
└── resources/
    ├── event-ids-reference.md
    ├── spl-cheatsheet.md
    ├── mitre-mapping.md
    └── glossary.md
```

---

## 🚀 HOW TO SHOWCASE THIS PORTFOLIO

### On Your Resume
```
HTB Academy SOC Analyst Certification (5/15 modules - 33.3%)
- Incident Response, SIEM, Windows Forensics, Threat Hunting, Splunk
- Hands-on labs with detailed technical documentation
- github.com/dheeraj2293/htb-soc-analyst-labs
```

### In Cover Letters
```
"I'm completing the Hack The Box SOC Analyst certification path,
actively documenting hands-on labs demonstrating mastery of incident
response, SIEM operations (Elastic & Splunk), forensic investigation,
and proactive threat hunting. Currently 33% complete with detailed
technical documentation of each module."
```

### During Interviews
1. **Open your GitHub** during the call
2. **Walk through Module 5** (Splunk):
   - "Here's how I wrote an SPL query to find Kerberos anomalies"
   - "This is how I correlated multiple log sources"
3. **Reference specific examples:**
   - Event IDs, query syntax, investigation methodology
4. **Discuss career trajectory:**
   - "I've progressed from basic response to proactive hunting"
   - "I understand both open-source and enterprise SIEM platforms"

---

## 🎓 INTERVIEW QUESTIONS YOU MASTER

**"How would you write a Splunk query to detect brute force attacks?"**
→ Explain EventCode 4625, stats count by src_ip, threshold-based alerting

**"What's the difference between Splunk and Elastic?"**
→ SPL vs. KQL, enterprise vs. open-source, deployment models, cost differences

**"Walk me through a real investigation you've done"**
→ Reference Kerberos case: identify anomalous account, corroborate across sources, timeline building

**"How do you reduce alert fatigue?"**
→ Hypothesis-driven hunting, proper threshold tuning, understanding baselines

**"Explain the 7-step threat hunting workflow"**
→ Planning, hypothesis, hunt design, data gathering, evaluation, mitigation, documentation

---

## 📊 SKILLS PROGRESSION CHART

| Skill | Module 1 | Module 2 | Module 3 | Module 4 | Module 5 |
|-------|----------|----------|----------|----------|----------|
| Incident Response | ✅ 85% | — | — | — | — |
| SIEM (Elastic) | — | ✅ 85% | — | ✅ 80% | — |
| Windows Forensics | — | — | ✅ 85% | — | — |
| Threat Hunting | — | — | — | ✅ 80% | — |
| SIEM (Splunk) | — | — | — | — | ✅ 85% |
| Log Analysis | — | ✅ 85% | ✅ 85% | ✅ 80% | ✅ 85% |
| **Overall SOC** | **40%** | **60%** | **75%** | **80%** | **85%** |

---

## ⏱️ COMPLETION TIMELINE

```
Modules Completed:     5/15 (33.3%)
Time Invested:         ~31 hours
Daily Pace:            ~5-6 hours/day
Modules per Day:       ~1 module every 6-8 hours

Projected Timeline:
- 50% by: End of day (3 more modules)
- 66% by: Tomorrow (5 more modules)  
- 100% by: Early September 2026

⚡ MOMENTUM IS STRONG! Keep this pace!
```

---

## 🏆 KEY ACHIEVEMENTS

✅ **5 of 15 modules complete** (33.3% progress)  
✅ **Both major SIEM platforms mastered** (Elastic + Splunk)  
✅ **Real investigations documented** (Kerberos case)  
✅ **Interview-ready technical knowledge**  
✅ **85% confidence for entry-level SOC roles**  

---

## 📞 Connect With Me

- **GitHub:** github.com/dheeraj2293
- **LinkedIn:** https://www.linkedin.com/in/dheeraj-kumar-repala-8171a5211/
- **Email:** dheerajrepala56@gmail.com
- **HTB Profile:** https://profile.hackthebox.com/profile/019f48fa-37a1-722f-a7a4-810a2b649801?utm_medium=copy_url

---

## 🛡️ Blue Team Philosophy

I'm building a career in **defensive security**—protecting systems, detecting threats, and responding to incidents. My focus is on:

✅ **Detection:** Finding threats using SIEM and forensics  
✅ **Investigation:** Understanding attacks with technical depth  
✅ **Response:** Acting quickly and appropriately  
✅ **Learning:** Continuous improvement and skill development  

**Not:** Red team, offensive security, or threat hunting for attackers.

---

*This portfolio is actively maintained. New module documentation added as coursework is completed. Follow progress at: github.com/dheeraj2293/htb-soc-analyst-labs*
