# HTB Academy SOC Analyst Job Role Path Portfolio

Complete hands-on documentation of the Hack The Box SOC Analyst certification path. This portfolio demonstrates practical cybersecurity skills in incident response, SIEM monitoring, Windows forensics, and threat detection.

**Target Role:** Entry-Level SOC Analyst (Blue Team)  
**Status:** 🟢 Active Learning (4/15 modules complete)  
**Progress:** 20% Complete  
**Last Updated:** August 2026

---

## 📊 Progress Dashboard

```
Overall Progress: ██████████████░░░░░░░░░░░░ 26.7% (4/15 modules complete)

Modules Completed:     ✅ 4
Modules In Progress:   🔄 0  
Modules Remaining:     ⏳ 11
Estimated Completion:  Early September 2026 (~18 days)

Recent Completions:
✅ Module 1: Incident Handling Process (Aug 2026)
✅ Module 2: Security Monitoring & SIEM Fundamentals (Aug 2026)
✅ Module 3: Windows Event Logs & Finding Evil (Aug 2026)
✅ Module 4:Introduction to Threat Hunting & Hunting With Elastic
```

---

## 📚 All 15 Modules Status

### ✅ COMPLETED (4/15)

#### **Module 1: Incident Handling Process** ✅
- **Date:** August 2026
- **Time:** ~8 hours
- **Rating:** ⭐⭐⭐⭐⭐ (5.0/5)
- **Key Learnings:**
  - 6-stage NIST incident response framework
  - Cyber Kill Chain attack lifecycle
  - Evidence handling and chain of custody
  - Post-incident root cause analysis
- **Case Study:** Insight Nexus 47-day data breach investigation
- **Skills:** Incident triage, escalation procedures, forensic documentation

[📖 Read Module 1 Full Writeup](./modules/01-incident-handling-process/README.md)

---

#### **Module 2: Security Monitoring & SIEM Fundamentals** ✅
- **Date:** August 2026
- **Time:** ~5 hours
- **Points:** 20 Cubes
- **Rating:** ⭐⭐⭐⭐⭐ (5.0/5)
- **Key Learnings:**
  - SIEM architecture and components
  - Elastic Stack pipeline (Beats → Logstash → Elasticsearch → Kibana)
  - SOC roles and responsibilities (Tier 1, 2, 3)
  - MITRE ATT&CK framework for detection
  - Alert triage methodology
  - SIEM visualization and dashboards
- **Case Studies:**
  - Brute force attack detection
  - RDP access monitoring
  - Failed logon analysis
  - Group membership changes
- **Skills:** Alert investigation, false positive identification, baseline understanding

[📖 Read Module 2 Full Writeup](./modules/02-security-monitoring-siem/README.md)

---

#### **Module 3: Windows Event Logs & Finding Evil** ✅ **NEW!**
- **Date:** August 2026
- **Time:** ~5 hours
- **Points:** 20 Cubes
- **Rating:** ⭐⭐⭐⭐⭐ (5.0/5)
- **Difficulty:** Medium (Tier 2)
- **Key Learnings:**
  - Windows Event Log types and critical Event IDs
  - Sysmon for advanced system monitoring
  - Event Tracing for Windows (ETW)
  - Get-WinEvent PowerShell cmdlet
  - Forensic investigation techniques
  - Real-world attack detection patterns
- **Sections Completed:**
  1. Introduction (Windows Event Logs, Sysmon Analysis)
  2. Additional Telemetry Sources (ETW, advanced monitoring)
  3. Analyzing Windows Event Logs En Masse (Get-WinEvent)
  4. Skills Assessment
- **Real Attack Examples:**
  - DLL hijacking detection
  - Credential dumping (Mimikatz)
  - PowerShell/C# injection
  - Unmanaged code injection
  - Process injection attacks
- **Skills:** Forensic timeline analysis, malware detection, persistence identification, log query optimization

[📖 Read Module 3 Full Writeup](./modules/03-windows-event-logs/README.md)

---
#### **Module 4: Introduction to Threat Hunting & Hunting with Elastic** ✅ **NEW!**
- **Date:** August 2026 | **Time:** ~5 hours | **Rating:** ⭐⭐⭐⭐⭐
- **Difficulty:** Medium (Tier 2)
- **Key Skills:** 
  - Hypothesis-driven hunting methodology
  - 7-step threat hunting workflow
  - Threat intelligence integration
  - IOC identification and use
  - Pyramid of Pain framework
  - Diamond Model of intrusion analysis
- **Tools:** Elastic Stack, Kibana Query Language (KQL), threat intelligence platforms
- **Real-World Applications:**
  - Dwell time reduction (average 200+ days → < 30 days with hunting)
  - Proactive threat discovery
  - APT campaign tracking
  - Emotet malware hunting example
- **Key Concepts Mastered:**
  - Difference between detection (reactive) vs. hunting (proactive)
  - TTPs (Tactics, Techniques, Procedures) for threat modeling
  - Behavior-based hunting vs. IOC-based hunting
  - Campaign and threat group analysis
- **Hunt Examples:**
  - Credential dumping campaigns
  - Lateral movement via admin shares
  - Persistence mechanism detection
  - C2 communication patterns

[📖 Read Module 4 Full Writeup](./modules/04-threat-hunting-&-threat-hunting-with-elastic/README.md)

### 🔄 IN PROGRESS (0/15)

**No modules currently in progress.**  
All completed modules fully documented. Ready for Module 5.

---

### ⏳ UPCOMING (12 modules)


#### **Module 5: Understanding Log Sources & Investigating With Splunk**
- Splunk fundamentals
- SPL query language
- Real-world investigations
- Log source analysis

#### **Module 6: Windows Attacks & Defense**
- Windows security mechanisms
- Common attack vectors
- Defense strategies
- Detection methods

#### **Modules 7-15: Advanced SOC Topics**
- Network traffic analysis (Modules 7-8)
- IDS/IPS systems (Module 9)
- Malware analysis (Module 10)
- JavaScript deobfuscation (Module 11)
- Detection rule writing (YARA/Sigma - Module 12)
- Digital forensics (Module 13)
- Advanced Splunk detection (Module 14)
- Incident reporting (Module 15)

---

## 🎯 CUMULATIVE SKILLS ACHIEVED

### Technical Skills (4 Modules, Progressive Building)

**Reactive Security Operations:**
- ✅ Incident response procedures (6-stage NIST)
- ✅ SIEM alert detection and triage
- ✅ Elastic Stack data pipeline
- ✅ Dashboard and visualization creation

**Investigation & Forensics:**
- ✅ Windows Event Log analysis
- ✅ Sysmon forensic data collection
- ✅ PowerShell event querying (Get-WinEvent)
- ✅ Timeline building and correlation
- ✅ Evidence preservation and chain of custody

**Proactive Threat Operations:**
- ✅ Hypothesis-driven threat hunting
- ✅ Elastic stack querying for threats
- ✅ Threat intelligence integration
- ✅ IOC identification and hunting
- ✅ Campaign-based analysis

**Frameworks & Standards:**
- ✅ NIST Incident Response Framework (6 stages)
- ✅ MITRE ATT&CK Framework (tactics & techniques)
- ✅ Cyber Kill Chain (attack lifecycle)
- ✅ Pyramid of Pain (indicator effectiveness)
- ✅ Diamond Model (intrusion analysis)
- ✅ Chain of Custody (evidence handling)

**Blue Team Competencies:**
- ✅ Defensive operations (detection + response)
- ✅ Forensic investigation (depth analysis)
- ✅ Proactive threat hunting (discovery)
- ✅ Log analysis and querying
- ✅ Incident triage and escalation
- ✅ Threat intelligence application

---

## 📈 CAREER READINESS PROGRESSION

```
After Module 1: 40% ready (know procedures)
After Module 2: 60% ready (know detection)
After Module 3: 75% ready (know investigation)
After Module 4: 85% ready (know proactive hunting)
Target: 95%+ after all 15 modules
```

**What You Can Do Now:**
- ✅ Respond to security incidents properly
- ✅ Understand how SIEM detects threats
- ✅ Investigate Windows systems forensically
- ✅ Hunt for threats proactively
- ✅ Answer most SOC analyst interview questions

---

## 💼 PORTFOLIO VALUE PROPOSITION

### Why This Portfolio Impresses Recruiters

**Shows:**
- Systematic, structured learning (not random)
- Hands-on practical skills (not just theory)
- Progressive skill building (26.7% complete, on track)
- Professional documentation (clear communication)
- Blue Team focus (what they need to hire)
- Real-world application knowledge

**Demonstrates:**
- Discipline (completing modules consistently)
- Technical depth (detailed explanations)
- Professional communication (documentation quality)
- Job-specific knowledge (SOC workflows understood)
- Commitment to cybersecurity career

---

## 📂 Repository Organization

```
htb-soc-analyst-labs/
│
├── README.md (this file - portfolio overview)
│
├── modules/
│   ├── 01-incident-handling-process/
│   │   ├── README.md (complete module 1 documentation)
│   │   ├── detailed-analysis.md
│   │   └── screenshots/
│   │
│   ├── 02-security-monitoring-siem/
│   │   ├── README.md (complete module 2 documentation)
│   │   ├── detailed-analysis.md
│   │   ├── siem-queries.md
│   │   └── screenshots/
│   │
│   ├── 03-windows-event-logs/
│   │   ├── README.md (complete module 3 documentation)
│   │   ├── event-id-reference.md
│   │   ├── powershell-queries.md
│   │   └── screenshots/
│   │
│   ├── 04-threat-hunting-elastic/ (coming soon)
│   ├── 05-splunk-fundamentals/ (coming soon)
│   └── [Modules 6-15] (coming soon)
│
└── resources/
    ├── event-id-reference.md (Windows Event Log IDs)
    ├── siem-queries.md (Splunk/Elastic examples)
    ├── powershell-queries.md (Investigation commands)
    ├── mitre-attack-mapping.md
    ├── glossary.md
    └── study-notes.md
```

---

## 🚀 How to Use This Portfolio

### For Job Applications
1. **Resume:**
   ```
   HTB Academy SOC Analyst Portfolio (4/15 modules - 26.7%)
   - Incident Response ✅ | SIEM Fundamentals ✅
   - Windows Forensics ✅ | Threat Hunting ✅
   - github.com/dheeraj2293/htb-soc-analyst-labs
   ```

2. **Cover Letter:**
   ```
   "I'm completing the Hack The Box SOC Analyst certification,
   actively documenting hands-on labs demonstrating practical skills
   in incident response, SIEM operations, forensic investigation,
   and proactive threat hunting. Visit [GitHub] to see my progress."
   ```

3. **LinkedIn:** Add portfolio link to Featured section

### During Interviews
1. Open GitHub live (show confidence in your work)
2. Walk through a module (demonstrate understanding)
3. Discuss a real attack scenario you documented
4. Explain investigation methodology
5. Reference specific Event IDs, queries, concepts

---

## 🎓 Interview Questions You Can Now Answer

**"Walk me through your understanding of incident response"**
→ Complete NIST 6-stage framework with escalation procedures

**"How does a SIEM detect threats?"**
→ Data pipeline from collection through alerting with Elastic Stack examples

**"How would you investigate a suspicious Windows process?"**
→ Event ID 4688 queries, Sysmon process details, timeline building

**"What's the difference between detection and hunting?"**
→ Detection = reactive (SIEM alerts), Hunting = proactive (hypothesis-driven search)

**"Describe how you'd hunt for credential theft."**
→ Hypothesis formation, Elastic queries for LSASS access, behavior pattern analysis

**"What's dwell time and why does it matter?"**
→ Attackers in network for 200+ days on average; hunting reduces it to <30 days

---

## 📊 Module-by-Module Value

| Module | What It Teaches | Job Impact | Confidence |
|--------|-----------------|-----------|-----------|
| 1 | How to respond | Essential procedures | 85% ✅ |
| 2 | How to detect | SIEM mastery | 85% ✅ |
| 3 | Where evidence comes from | Forensic depth | 85% ✅ |
| 4 | How to hunt | Proactive skills | 80% ✅ |
| 5 | Splunk SIEM | Commercial SIEM mastery | TBD ⏳ |
| 6 | Windows attacks | Understanding attack methods | TBD ⏳ |
| 7-8 | Network analysis | Network-based detection | TBD ⏳ |
| 9 | IDS/IPS | Intrusion systems | TBD ⏳ |
| 10 | Malware analysis | Malicious file investigation | TBD ⏳ |
| 11 | JavaScript deobfuscation | Script analysis | TBD ⏳ |
| 12 | Detection rules | Custom detection creation | TBD ⏳ |
| 13 | Digital forensics | Deep investigation | TBD ⏳ |
| 14 | Splunk advanced | Windows threat detection | TBD ⏳ |
| 15 | Incident reporting | Professional documentation | TBD ⏳ |

---

## ✨ Achievements Summary

**Learning Progress:**
- ✅ Foundation complete (Modules 1-4)
- ✅ Core SOC competencies established
- ✅ Ready for specialized modules
- ✅ Can explain incident response → detection → investigation chain

**Documentation Quality:**
- ✅ Each module thoroughly explained
- ✅ Real-world examples included
- ✅ Interview-ready answers prepared
- ✅ Technical details documented

**Career Readiness:**
- ✅ 85% confident for entry-level SOC role
- ✅ Can discuss practical scenarios
- ✅ Understand security operations workflow
- ✅ Can perform basic forensic investigation

---

## 🔗 Important Links

- **Hack The Box Academy:** https://academy.hackthebox.com
- **NIST Cybersecurity Framework:** https://www.nist.gov/cyberframework
- **MITRE ATT&CK Framework:** https://attack.mitre.org
- **Cyber Kill Chain:** https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html

---

## 👤 About This Portfolio

**Created By:** Dheeraj Kumar Repala  
**Target Position:** Entry-Level SOC Analyst (Blue Team)  
**Status:** 🟢 Actively Learning & Documenting  
**Completion:** 26.7% (4 of 15 modules)  
**Last Updated:** August 2026

This portfolio demonstrates:
- Systematic technical training (not random learning)
- Hands-on lab completion (practical skills)
- Professional documentation (communication ability)
- Job-specific knowledge (what SOC analysts actually do)
- Progressive skill building (20% complete, on track)

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
