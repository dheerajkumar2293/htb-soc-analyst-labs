# 🎯 Threat Hunting & Threat Detection Module - Completion Portfolio

![Threat Hunting](https://img.shields.io/badge/Threat%20Hunting-Certified-brightgreen?style=for-the-badge)
![Elastic Stack](https://img.shields.io/badge/Elastic%20Stack-Proficient-blue?style=for-the-badge)
![SIEM](https://img.shields.io/badge/SIEM-Security%20Analysis-red?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)

## ✅ Module Completion Certificate

**Module**: Introduction to Threat Hunting & Hunting With Elastic  
**Platform**: HackTheBox (HTB Academy)  
**Difficulty**: Medium (Tier 2)  
**Estimated Duration**: 4 hours  
**Points Earned**: 20 Cubes 🟩🟩🟩🟩🟩  
**Rating**: ⭐⭐⭐⭐⭐ (5.0/5)  
**Status**: ✅ **COMPLETED**  

---

## 📚 Module Overview

This comprehensive module covers threat hunting fundamentals, threat intelligence, and practical threat hunting using Elastic Stack SIEM platform. The module combines theory with hands-on exercises using real-world attack scenarios.

### Module Structure

The course is organized into **6 sections** with **5 interactive labs**:

#### **Section 1: Threat Hunting & Threat Intelligence Fundamentals** ✅
- ✓ Interactive: Threat Hunting Fundamentals
- ✓ Interactive: The Threat Hunting Process
- ✓ Theory: Threat Hunting Glossary
- ✓ Interactive: Threat Intelligence Fundamentals

**Key Concepts Learned**:
- Definition and importance of threat hunting
- Threat hunting vs threat detection
- The 8-step threat hunting methodology
- Pyramid of Pain framework
- Diamond Model for threat analysis
- Cyber Threat Intelligence (CTI) types
- IOC (Indicators of Compromise) analysis

#### **Section 2: Threat Hunting With The Elastic Stack** ✅
- ✓ Interactive: Hunting For Stuxbot

**Hands-On Lab**:
- Conducted real threat hunting investigation
- Used Kibana Discover for log analysis
- Wrote KQL (Kibana Query Language) queries
- Identified attack indicators
- Reconstructed attack timeline

#### **Section 3: Let's Go Hunting** ✅
- ✓ Interactive: Skills Assessment

**Assessment Completed**:
- 3 hunt challenges passed
- Real-world Stuxbot threat analysis
- Complete investigation results

---

## 🔍 Stuxbot Case Study - Hunt Results

### Investigation Summary

**Threat Actor**: Stuxbot (Organized Cybercrime Collective)  
**Target**: Microsoft Windows Infrastructure  
**Objective**: Domain Controller Compromise via Multi-Stage Attack  
**Status**: ✅ **THREATS DETECTED & ANALYZED**  

---

## 🎯 Hunt 1: Lateral Tool Transfer (T1570)

**MITRE ATT&CK Technique**: [T1570 - Lateral Tool Transfer](https://attack.mitre.org/techniques/T1570/)

### Objective
Identify malicious tool transfer to staging directory used for attack preparation.

### Investigation Details
- **Event Type**: Sysmon Event ID 11 (File Create)
- **Location**: C:\Users\Public\ (World-writable staging directory)
- **Tool Detected**: Rubeus.exe (Kerberos manipulation utility)
- **User Account**: svc-sql1 (Compromised service account)
- **Time**: March 26, 2023

### KQL Query Used
```kql
event.code:11 AND winlog.event_data.TargetFilename:*public*
```

### Hunt Result
✅ **THREAT FOUND**
```
Tool Name: Rubeus.exe
Location: C:\Users\Public\Rubeus.exe
User: svc-sql1
Status: Lateral tool transfer confirmed
```

### Answer Submitted
```
svc-sql1
```

### Security Implication
- Service account used for tool staging
- Indicates pre-compromise for later execution
- Access to Kerberos manipulation tools suggests domain attack planning

---

## 🎯 Hunt 2: Registry Run Keys Persistence (T1547.001)

**MITRE ATT&CK Technique**: [T1547.001 - Boot or Logon Autostart Execution: Registry Run Keys](https://attack.mitre.org/techniques/T1547/001/)

### Objective
Detect registry-based persistence mechanism ensuring malware survives system reboots.

### Investigation Details
- **Event Type**: Sysmon Event ID 13 (Registry Object Set Value)
- **Registry Path**: HKLM\Software\Microsoft\Windows\CurrentVersion\Run
- **Registry Value**: LgvHsv1AUVTsIN (Suspicious value name)
- **Purpose**: Auto-start malware on system boot
- **Time**: March 26, 2023 @ 20:17:33

### KQL Query Used
```kql
event.code:13 AND registry.path:"*Run*"
```

### Hunt Result
✅ **PERSISTENCE MECHANISM FOUND**
```
Registry Value: LgvHsv1AUVTsIN
Registry Path: *CurrentVersion\Run
Status: Registry persistence confirmed
Severity: CRITICAL
```

### Answer Submitted
```
LgvHsv1AUVTsIN
```

### Security Implication
- Malware survives system reboots
- Executed with system/admin privileges
- Indicates mature, sophisticated malware infrastructure
- Difficult to detect without registry monitoring

---

## 🎯 Hunt 3: PowerShell Remoting Lateral Movement (T1021.006)

**MITRE ATT&CK Technique**: [T1021.006 - Remote Service Session Initiation: Windows Remote Management](https://attack.mitre.org/techniques/T1021/006/)

### Objective
Identify PowerShell remoting abuse for lateral movement to Domain Controller.

### Investigation Details
- **Process**: powershell.exe
- **Protocol**: WinRM (Windows Remote Management)
- **Ports**: 5985 (HTTP), 5986 (HTTPS)
- **Target**: DC1 (Domain Controller)
- **User Account**: svc-sql1 (Compromised account)
- **Time**: March 26-27, 2023

### KQL Query Used
```kql
process.name:powershell.exe AND destination.address:*DC1*
```

### Hunt Result
✅ **LATERAL MOVEMENT TO DC CONFIRMED**
```
Process: powershell.exe
Destination: DC1 (Domain Controller)
Port: 5985/5986 (WinRM)
User: svc-sql1
Status: PowerShell remoting lateral movement confirmed
Impact: CRITICAL - Domain controller access achieved
```

### Answer Submitted
```
svc-sql1
```

### Security Implication
- Direct path to domain takeover
- Compromised service account used
- Attacker gains domain controller access
- Full network compromise likely if not detected

---

## 📊 Complete Attack Chain Analysis

```
ATTACK TIMELINE
───────────────────────────────────────────────────────────

Mar 26, 2023 - INITIAL COMPROMISE
│
├─→ Phishing email delivered
│
├─→ OneNote attachment (invoice.one) opened
│
└─→ Embedded batch script executed

Mar 26, 2023 - TOOL STAGING (Hunt 1 ✓)
│
├─→ PowerShell downloads payload
│
├─→ Rubeus.exe staged in C:\Users\Public\
│
└─→ User: svc-sql1

Mar 26, 2023 20:17:33 - PERSISTENCE (Hunt 2 ✓)
│
├─→ Registry modified (Event ID 13)
│
├─→ Run key: LgvHsv1AUVTsIN
│
└─→ Malware auto-starts on boot

Mar 26-27, 2023 - LATERAL MOVEMENT (Hunt 3 ✓)
│
├─→ PowerShell remoting initiated
│
├─→ WinRM connection to DC1 (5985/5986)
│
├─→ Using svc-sql1 credentials
│
└─→ Domain Controller COMPROMISED ⚠️

RESULT: COMPLETE NETWORK COMPROMISE
```

---

## 📈 Hunt Assessment Results

| Hunt | Technique | Status | Confidence | Answer |
|------|-----------|--------|-----------|--------|
| **Hunt 1** | T1570 (Lateral Tool Transfer) | ✅ Passed | HIGH | svc-sql1 |
| **Hunt 2** | T1547.001 (Registry Persistence) | ✅ Passed | HIGH | LgvHsv1AUVTsIN |
| **Hunt 3** | T1021.006 (PowerShell Remoting) | ✅ Passed | HIGH | svc-sql1 |

### Overall Assessment: ✅ **PASSED - ALL HUNTS SUCCESSFUL**

---

## 🎓 Skills Demonstrated

### Technical Skills Acquired

✅ **SIEM Proficiency**
- Kibana Discover interface navigation
- Log aggregation and search
- Real-time alert investigation
- Historical data analysis

✅ **Query Language Expertise**
- KQL (Kibana Query Language) syntax
- Building complex queries
- Field-based filtering
- Boolean logic operators

✅ **Log Analysis**
- Windows Event Log analysis
- Sysmon event interpretation
- Registry modification tracking
- Process execution monitoring
- Network connection analysis

✅ **Threat Hunting**
- Hypothesis formulation
- Systematic threat investigation
- Evidence correlation
- Timeline reconstruction
- IOC identification

✅ **Attack Analysis**
- MITRE ATT&CK technique mapping
- Attack chain analysis
- Threat actor behavior analysis
- Compromise indicators
- Impact assessment

### Analytical Skills Acquired

✅ **Critical Thinking**
- Problem decomposition
- Hypothesis testing
- Evidence validation
- False positive elimination

✅ **Investigation Methodology**
- 8-step hunting process
- Systematic data review
- Pattern recognition
- Correlation analysis

✅ **Security Analysis**
- Risk assessment
- Impact evaluation
- Incident severity determination
- Remediation recommendation

### Professional Skills Acquired

✅ **Documentation**
- Finding recording and evidence capture
- Timeline documentation
- Report writing
- Security findings presentation

✅ **Communication**
- Technical finding explanation
- Stakeholder reporting
- Security risk articulation

---

## 🛠️ Tools & Technologies Mastered

### SIEM Platforms
- ✅ **Elastic Stack** (Elasticsearch + Kibana)
- ✅ **Log Analysis** (Discover, Explore)
- ✅ **Query Building** (KQL)

### Log Sources
- ✅ Windows Event Logs
- ✅ Sysmon (System Monitor)
- ✅ PowerShell Logs
- ✅ Zeek Network Logs

### Security Concepts
- ✅ MITRE ATT&CK Framework
- ✅ Indicators of Compromise (IOCs)
- ✅ Pyramid of Pain
- ✅ Diamond Model
- ✅ Attack Chain Analysis

---

## 📋 Module Content Breakdown

### Topics Covered

**Fundamentals**
- Threat hunting definition and importance
- Threat hunting vs threat detection
- Threat intelligence fundamentals
- CTI types (Strategic, Tactical, Operational)
- Intelligence analysis framework

**Processes & Frameworks**
- 8-step threat hunting methodology
- MITRE ATT&CK enterprise framework
- Attack lifecycle phases
- Incident handling procedures
- Risk assessment frameworks

**Practical Application**
- Real-world threat scenario (Stuxbot)
- Multi-stage attack analysis
- Evidence correlation techniques
- Timeline reconstruction
- Reporting and documentation

**SIEM Operations**
- Elastic Stack architecture
- Kibana interface and features
- Log ingestion and parsing
- Query construction
- Search optimization

---

## 💼 Career Relevance

### Roles Aligned With Skills

**🟢 SOC Analyst Level 1-2**
- Alert triage and investigation
- Log analysis and searching
- Incident response support
- Documentation and reporting

**🟡 Threat Hunter**
- Proactive threat investigation
- Hypothesis-driven hunting
- IOC identification
- Detection rule development

**🟡 Security Analyst**
- SIEM operations
- Event correlation
- Risk assessment
- Incident analysis

**🟡 Incident Responder**
- Timeline reconstruction
- Evidence collection
- Attack analysis
- Breach investigation

### Certifications Aligned

This completion aligns with knowledge required for:
- ✅ CompTIA Security+
- ✅ CompTIA CySA+
- ✅ GCIH (GIAC Certified Incident Handler)
- ✅ ECIH (EC-Council Incident Handler)
- ✅ OSCP (Offensive Security)

---

## 🚀 Practical Achievements

### Real Investigation Completed
```
✓ Identified malicious tool staging
✓ Detected persistence mechanisms  
✓ Found lateral movement evidence
✓ Reconstructed complete attack chain
✓ Determined impact level (CRITICAL)
✓ Identified affected systems (DC1)
✓ Pinpointed compromised account (svc-sql1)
```

### Queries Written & Validated
```
✓ File creation event queries
✓ Registry modification queries
✓ Process and network queries
✓ All queries returned relevant results
✓ Evidence successfully extracted
```

### Documentation Produced
```
✓ Hunt methodology documentation
✓ Attack timeline reconstruction
✓ Evidence collection records
✓ Finding summaries and analysis
✓ Threat assessment reports
```

---

## 📊 Module Statistics

| Metric | Value |
|--------|-------|
| **Total Sections** | 6 |
| **Interactive Labs** | 5 |
| **Estimated Hours** | 4 |
| **Difficulty Level** | Medium (Tier 2) |
| **Points Earned** | 20 Cubes |
| **Rating** | ⭐⭐⭐⭐⭐ (5.0/5) |
| **Hunts Completed** | 3/3 |
| **Success Rate** | 100% |
| **Skills Assessed** | 10+ |

---

## 🔗 Related Resources

### MITRE ATT&CK Framework
- [T1570 - Lateral Tool Transfer](https://attack.mitre.org/techniques/T1570/)
- [T1547.001 - Registry Run Keys](https://attack.mitre.org/techniques/T1547/001/)
- [T1021.006 - Windows Remote Management](https://attack.mitre.org/techniques/T1021/006/)
- [Full Enterprise ATT&CK Matrix](https://attack.mitre.org/matrices/enterprise/)

### Elastic Stack Documentation
- [Kibana Query Language (KQL)](https://www.elastic.co/guide/en/kibana/current/kuery-query.html)
- [Elastic Stack Documentation](https://www.elastic.co/guide/index.html)
- [Sysmon Field Reference](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-sysmon.html)

### Learning Resources
- [HackTheBox Academy](https://academy.hackthebox.com/)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)
- [Splunk Cyber Security](https://www.splunk.com/en_us/training.html)

---

## 🎖️ Certifications & Badges

### Module Completion Badge
```
╔══════════════════════════════════════════════════════╗
║  ✅ Introduction to Threat Hunting & Elastic Stack   ║
║                                                      ║
║  Completed: 2024                                     ║
║  Difficulty: Medium (Tier 2)                         ║
║  Rating: ⭐⭐⭐⭐⭐                                  ║
║  Points: 20 Cubes                                    ║
║                                                      ║
║  Skills Verified:                                    ║
║  ✓ Threat Hunting                                    ║
║  ✓ SIEM Analysis                                     ║
║  ✓ KQL Query Writing                                 ║
║  ✓ Attack Investigation                              ║
║  ✓ Timeline Reconstruction                           ║
╚══════════════════════════════════════════════════════╝
```

---

## 📝 Key Takeaways

### What I Learned

1. **Threat Hunting Is Systematic**
   - Follows structured 8-step methodology
   - Hypothesis-driven approach yields results
   - Correlation of multiple events essential

2. **SIEM Is Powerful**
   - Centralized logging enables investigation
   - Query language allows precision searches
   - Real-time and historical analysis possible

3. **Attack Chains Are Complex**
   - Multiple techniques chained together
   - Service accounts valuable targets
   - Persistence is attacker priority

4. **Evidence Matters**
   - Specific fields provide answers
   - Timestamp ordering reveals sequence
   - Multiple data sources needed

5. **Documentation Is Critical**
   - Clear reporting enables response
   - Timeline reconstruction shows impact
   - Findings drive security improvements

---

## 🎯 Next Steps

### Continuing Education
- [ ] Complete advanced threat hunting courses
- [ ] Master additional SIEM platforms (Splunk, Sentinel)
- [ ] Study MITRE ATT&CK in depth
- [ ] Practice with CTF challenges
- [ ] Build threat detection rules

### Portfolio Building
- [ ] Document findings professionally
- [ ] Create GitHub security projects
- [ ] Write threat analysis reports
- [ ] Share knowledge with community
- [ ] Contribute to open source security

### Career Development
- [ ] Pursue relevant certifications
- [ ] Apply for analyst positions
- [ ] Join security team or SOC
- [ ] Mentor others in field
- [ ] Specialize in threat hunting

---

## 📞 Summary

This module completion demonstrates proficiency in:

✅ **Threat Hunting** - Proactive threat investigation  
✅ **SIEM Analysis** - Log analysis and correlation  
✅ **Attack Analysis** - Understanding threat chains  
✅ **Investigation** - Systematic evidence discovery  
✅ **Documentation** - Professional reporting  

**Ready for roles in**: SOC Analysis, Threat Hunting, Incident Response, Security Analysis

---

## 📄 Project Files

This repository contains documentation from the module completion:

- `THREAT-DETECTION-HUNTING-COMPLETE-GUIDE.md` - Comprehensive threat hunting guide
- `STUXBOT-ROUND-2-README.md` - Stuxbot case study documentation
- `STUXBOT-ROUND-2-GUIDE.md` - Detailed hunt walkthrough
- `STUXBOT-ROUND-2-QUICK-REF.txt` - Quick reference for queries

---

## 🏆 Conclusion

Successfully completed the "Introduction to Threat Hunting & Hunting With Elastic" module with 100% hunt success rate. Gained practical experience in threat detection, SIEM operations, and real-world attack analysis. Ready to apply these skills in professional security roles.

**Status**: ✅ **CERTIFIED & READY**

---

**Created**: 2024  
**Platform**: HackTheBox Academy  
**Module**: Threat Hunting & Elastic Stack  
**Difficulty**: Medium (Tier 2)  
**Rating**: ⭐⭐⭐⭐⭐  

---

*"Hunt threats proactively, analyze attacks systematically, defend decisively."* 🎯

**Ready to hunt!** 🚀

<img width="1002" height="440" alt="image" src="https://github.com/user-attachments/assets/c2625ac4-5c4f-48f2-8733-f489cb0db6ba" />
<img width="972" height="611" alt="image" src="https://github.com/user-attachments/assets/e2888dd8-16ac-46cd-8f03-2873e8aca921" />
