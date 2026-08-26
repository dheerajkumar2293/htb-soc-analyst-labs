# MODULE 3: Windows Event Logs & Finding Evil
## Complete Documentation & Analysis

**Status:** ✅ COMPLETED  
**Date Completed:** August 2026  
**Difficulty:** Medium (Tier 2)  
**Time Investment:** ~5 hours  
**Points Earned:** 20 Cubes  
**Rating:** ⭐⭐⭐⭐⭐ (5.0/5 stars)  
**Sections:** 6 sections, 5 interactive exercises, 1 assessment
**Assessment Result:** ✅ PASSED

---

## 📚 EXECUTIVE SUMMARY

**Module 3 is the BRIDGE between theory and forensic investigation.**

- **Module 1** taught incident response (how to handle incidents)
- **Module 2** taught detection (how SIEM finds threats)
- **Module 3** teaches investigation (where evidence comes from)

This module answered the critical question: **"Where do the detections come from?"** Answer: **Windows Event Logs and Sysmon.**

In enterprise environments, 90%+ of systems run Windows. Windows Event Logs are the **primary data source** for SOC detection. This module taught me how to read, analyze, and hunt through that data.

---

## 🎯 WHY THIS MODULE MATTERS

**Before Module 3:** I knew SIEM collects logs and creates alerts.
**After Module 3:** I understand EXACTLY what logs are collected and how to analyze them forensically.

**Key Understanding:**
```
Real Attack → Windows generates Event Logs → SIEM collects logs 
→ Alert rule triggered → SOC analyst investigates → You find evidence
```

This module teaches **that entire chain**, specifically YOUR investigation part.

---

## 📋 WHAT I LEARNED (Section by Section)

### Section 1: Introduction ✅ COMPLETED

#### 1.1 - Windows Event Logs (Interactive)

**What I Learned:**

Windows Event Logs are the **operating system's security journal**. Every important action gets logged.

**Key Event Log Types:**

| Log Type | What It Records | Example Events |
|----------|-----------------|-----------------|
| **Security** | Authentication, access, privilege changes | Login attempts, file access, permission changes |
| **System** | OS events, driver loads, service starts | System restart, driver failure, service crash |
| **Application** | Application errors and events | Software crash, license expiration |
| **PowerShell** | PowerShell command execution | Scripts run, commands executed |
| **Sysmon** (custom) | Advanced system monitoring | Process creation, file writes, network connections |

**Critical Event IDs (What SOC Analysts Look For):**

| Event ID | Meaning | Investigation |
|----------|---------|---|
| **4624** | Successful login | Who logged in? From where? |
| **4625** | Failed login | Brute force attack? |
| **4688** | Process created | What did the process do? |
| **4689** | Process terminated | Was it killed? By whom? |
| **4728** | User added to group | Privilege escalation? |
| **4732** | User added to local group | Admin access granted? |
| **4743** | Computer account changed | System compromised? |
| **5156** | Network connection | Data exfiltration? C2 communication? |

**Real Example Investigation:**
```
Alert: "Suspicious process creation"
Event ID: 4688
Details:
- Process: powershell.exe
- Parent Process: explorer.exe
- Command Line: "powershell.exe -ExecutionPolicy Bypass -File C:\temp\script.ps1"
- User: admin
- Timestamp: 02:45 AM (unusual time)

My Investigation:
1. Why is PowerShell running from Explorer? (unusual)
2. What's in C:\temp\script.ps1? (malware check)
3. Why at 2:45 AM? (after hours = suspicious)
4. What did the script do? (look for follow-on events)

Conclusion: Likely lateral movement or privilege escalation
Response: Isolate system, analyze script, check for data theft
```

**My Understanding:**
Windows Event Logs are like a **detailed security camera recording every action**. My job as SOC analyst is to review the footage and spot the attackers.

---

#### 1.2 - Analyzing Evil With Sysmon & Event Logs (Interactive)

**What I Learned:**

**Sysmon = Supercharged Windows Logging**

Standard Windows Event Logs are good. Sysmon is BETTER.

**What Sysmon Monitors:**
1. **Process Creation** - Every process started with full command line
2. **Process Termination** - When processes die and how
3. **File Creation & Write Time Changes** - File modifications
4. **Registry Events** - Registry key modifications (malware persistence method)
5. **Network Connections** - Outbound connections (C2 communication, data exfiltration)
6. **Image Load Events** - DLL loaded by process (DLL injection, malware loading)
7. **CreateRemoteThread** - Process creating thread in another process (code injection)
8. **Raw Access Thread** - Low-level disk access (forensics evasion)

**Why Sysmon Matters:**
Standard Event Logs = Basic information  
Sysmon = Detailed forensic evidence

**Real Detection Example (DLL Hijacking):**

```
Malware Attack: DLL Hijacking
Attacker created malicious DLL named "mscoree.dll" in C:\temp
When legitimate application looks for mscoree.dll, it loads malicious version

WITHOUT Sysmon:
- No indication malicious DLL was created
- No indication malicious DLL was loaded

WITH Sysmon:
Event: File Creation
- File: C:\temp\mscoree.dll
- Creator: admin process

Event: Image Load
- Process: notepad.exe
- DLL Loaded: C:\temp\mscoree.dll

Analysis: CAUGHT! Malicious DLL detected and identified.
```

**My Insight:**
Sysmon is like upgrading from security camera to **HD security camera with motion tracking**. It catches what standard logs miss.

**How Sysmon Helps Detection:**
```
1. Attack happens on Windows system
2. Sysmon records EVERYTHING
3. SIEM ingests Sysmon logs
4. Detection rules trigger on malicious patterns
5. SOC analyst (me) investigates with detailed Sysmon evidence
```

---

### Section 2: Additional Telemetry Sources ✅ COMPLETED

#### 2.1 - Event Tracing for Windows (ETW) (Theory)

**What I Learned:**

ETW (Event Tracing for Windows) is another **advanced data source** for threat detection.

**ETW vs Sysmon:**
- **Sysmon:** Logs to Windows Event Logs (you can query with PowerShell)
- **ETW:** Real-time tracing (raw event stream)

**ETW Use Case:**
ETW is used for:
- Real-time threat monitoring (happens as it occurs)
- Low-level system behavior (before it's written to logs)
- Defensive monitoring (immediate response capability)

**Real Example:**
```
Process Injection Attack:
1. Attacker process wants to inject code into another process
2. Uses CreateRemoteThread API call
3. Sysmon records this (later analysis)
4. ETW captures this in REAL-TIME
5. Detection tool alerts immediately
```

**My Understanding:**
ETW is like **real-time threat monitoring** vs. Sysmon's detailed **forensic investigation**. Both needed.

---

#### 2.2 - Tapping Into ETW (Interactive)

**What I Learned:**

How to actually access ETW data for threat hunting.

**Tools to Access ETW:**
- Windows Event Viewer (GUI)
- Get-WinEvent (PowerShell)
- SIEM tools (Splunk, Elastic)
- ETW Trace Providers

**Real Use:**
```
Scenario: Detect credential theft
Technique: Monitor LSASS.exe process access
ETW Provider: Security - Process Access Monitoring

PowerShell Query:
Get-WinEvent -FilterHashtable @{
  LogName='Security'
  ID=4656
  Data='lsass.exe'
}
```

---

### Section 3: Analyzing Windows Event Logs En Masse ✅ COMPLETED

#### 3.1 - Get-WinEvent (Interactive)

**What I Learned:**

**Get-WinEvent** = PowerShell tool to query/analyze Windows Event Logs at scale.

**Why It Matters:**
- Can't manually review 10,000+ events
- Need to automate analysis
- Get-WinEvent lets you query efficiently

**Real Investigation Example:**

**Scenario:** Detect all failed logins in last 24 hours

**PowerShell Command:**
```powershell
Get-WinEvent -FilterHashtable @{
  LogName='Security'
  ID=4625
  StartTime=(Get-Date).AddHours(-24)
} | Select-Object TimeCreated, Message | Sort-Object TimeCreated -Descending
```

**Output Analysis:**
```
TimeCreated: 2026-08-20 14:32:15
Message: Failed logon. Account: admin, IP: 192.168.1.100

TimeCreated: 2026-08-20 14:32:32
Message: Failed logon. Account: admin, IP: 192.168.1.100

TimeCreated: 2026-08-20 14:32:49
Message: Failed logon. Account: admin, IP: 192.168.1.100

[... 47 more from same IP ...]

My Analysis:
- 50 failed logins from same IP (192.168.1.100)
- All to same account (admin)
- All within 1 minute
- Clearly a brute force attack
```

**Action:**
1. Block IP 192.168.1.100 at firewall
2. Reset admin password
3. Check for successful logins (breach confirmation)
4. Escalate to Tier 2 for further investigation

**My Understanding:**
Get-WinEvent lets me **hunt through gigabytes of logs** and find exactly what I'm looking for. Without it, analysis would be impossible.

---

### Section 4: Skills Assessment ✅ COMPLETED & PASSED

**Assessment Overview:**
Interactive quiz testing knowledge of all 4 sections.

**Topics Tested:**
✅ Windows Event Log types and Event IDs
✅ Sysmon functionality and benefits
✅ Event Tracing for Windows (ETW)
✅ Get-WinEvent PowerShell syntax
✅ Forensic investigation techniques
✅ Real-world attack detection

**Key Questions & Answers:**

**Q1: "What Event ID indicates a failed login attempt?"**
My Answer: "Event ID 4625 in Security log"
Assessment: ✅ CORRECT

**Q2: "How would you detect DLL hijacking using Sysmon?"**
My Answer: "Look for Image Load events showing DLL loaded from unusual location (not System32). Monitor for process loading DLL from temp folders or user directories."
Assessment: ✅ CORRECT

**Q3: "What does Sysmon provide that standard Event Logs don't?"**
My Answer: "Sysmon provides process command lines, network connections, registry modifications, file creation, DLL loading, and code injection attempts—all with forensic detail."
Assessment: ✅ CORRECT

**Q4: "How would you query failed logins using Get-WinEvent?"**
My Answer: "Use Get-WinEvent with filter for LogName='Security', ID=4625, filtered by timeframe"
Assessment: ✅ CORRECT

**Q5: "Describe how you'd investigate PowerShell injection attack."**
My Answer: "Look for Event ID 4688 (process creation) showing powershell.exe. Review command line for suspicious parameters. Check parent process (should be user action, not malware). Use Sysmon ImageLoad events to identify DLLs loaded by PowerShell."
Assessment: ✅ CORRECT

**Assessment Result:** ✅ **PASSED**

---

## 🔍 MY DETAILED ANALYSIS

### Real-World Attack Detection (What I Learned)

**Attack 1: Credential Dumping (Mimikatz)**

```
Attack: Attacker runs Mimikatz to dump NTLM hashes from LSASS.exe

Windows Events Generated:
1. Event 4688: Process Creation
   - Process: cmd.exe
   - Command: C:\temp\mimikatz.exe
   - Parent: explorer.exe

2. Event 4688: Process Creation  
   - Process: mimikatz.exe
   - Command: "privilege::debug" "sekurlsa::logonpasswords" "exit"

3. Sysmon Event: File Creation
   - File: C:\temp\output.txt (dumped hashes)

My Investigation:
1. Why is mimikatz.exe running? (malware!)
2. Accessing LSASS = password theft
3. Output written to temp file = storing stolen data
4. Conclusion: Credential compromise

Response:
1. Isolate system immediately
2. Reset all user passwords
3. Check for lateral movement (did attacker use stolen creds?)
4. Force password reset for affected users
```

**Attack 2: DLL Hijacking**

```
Attack: Attacker creates fake mscoree.dll in C:\temp
When legitimate app loads it, malware runs

Windows Events Generated:
1. Sysmon Event: File Creation
   - File: C:\temp\mscoree.dll
   - Creator: User account

2. Sysmon Event: Image Load
   - Process: notepad.exe
   - DLL: C:\temp\mscoree.dll (WRONG LOCATION!)

My Investigation:
1. Standard Windows: No indication of malicious DLL
2. With Sysmon: Clear evidence of DLL hijacking
3. Found malicious DLL in wrong location
4. Identified process that loaded it

Response:
1. Delete malicious DLL
2. Scan for other DLL hijacking attempts
3. Enable DLL Search Order Randomization (ASLR)
4. Monitor for recurrence
```

**Attack 3: PowerShell Injection (Unmanaged Code)**

```
Attack: Attacker injects PowerShell code into legitimate process

Windows Events Generated:
1. Event 4688: Process Creation
   - Process: powershell.exe
   - Parent: explorer.exe (SUSPICIOUS!)
   - CommandLine: -ExecutionPolicy Bypass -File malicious.ps1

2. Event 4103: PowerShell Script Block Logging
   - Shows actual commands executed
   - Example: "New-ItemProperty -Path HKLM:\Software -Name backdoor"

My Investigation:
1. Why is PowerShell running from Explorer? (unusual)
2. What's in the script? (code injection detected)
3. What registry changes? (persistence mechanism)
4. Conclusion: Attack in progress

Response:
1. Block PowerShell execution from Explorer
2. Kill malicious process
3. Remove registry backdoor
4. Check for similar attacks
```

---

## 💡 CRITICAL INSIGHTS GAINED

### Insight 1: Windows Logs are Evidence
Just like a crime scene investigation:
- Logs = evidence
- Event IDs = types of evidence
- Timeline = establishing sequence
- My job = connect the dots

### Insight 2: Sysmon is Non-Negotiable
Standard Windows logging misses 50%+ of attacks.
Sysmon catches what standard logging doesn't.
Professional SOCs ALWAYS have Sysmon enabled.

### Insight 3: Forensic Detail Matters
Same attack can look different in logs:
- Attacker A might use Process 1 → Process 2
- Attacker B might use Process 3 → Process 4

But both leave forensic traces. My job is finding them.

### Insight 4: PowerShell is a Double-Edged Sword
- Legitimate use: Systems management
- Attacker use: Code execution, lateral movement, persistence

Need to monitor everything PowerShell does.

### Insight 5: Investigation Speed Matters
Can I quickly answer: "Was this system compromised?"

With Get-WinEvent queries, I can answer in minutes vs. hours.

---

## 📊 SKILL BREAKDOWN

| Skill | Before | After | Confidence |
|-------|--------|-------|-----------|
| Understanding Windows Event Logs | 30% | 90% | Very High |
| Identifying malicious Event IDs | 20% | 85% | High |
| Using Sysmon for detection | 10% | 80% | High |
| PowerShell investigation queries | 15% | 75% | High |
| Forensic timeline analysis | 25% | 80% | High |
| Credential theft detection | 20% | 85% | High |
| Malware persistence detection | 15% | 80% | High |
| Process injection identification | 10% | 75% | High |

---

## 🎓 INTERVIEW-READY ANSWERS

**Q: "How would you investigate a potential breach on a Windows system?"**

My Answer (Now):
"I'd start by querying Windows Event Logs for suspicious activities. First, check for unusual logins (Event 4625 - failed attempts, Event 4624 - successful from odd locations). Check Sysmon for process creation events (4688) looking for malware or unusual parent-child relationships. Look for persistence mechanisms like scheduled tasks or registry modifications. Query Get-WinEvent to find all suspicious events in a timeframe. Then correlate timeline to understand what happened when. If I find evidence of compromise, I escalate to Tier 2 for password resets and further forensic analysis."

---

**Q: "How do you detect credential dumping attacks?"**

My Answer (Now):
"Monitor for access to LSASS.exe process. Look for Event 4656 (process access), specifically for tools like Mimikatz. Check for unusual processes accessing LSASS—legitimate tools rarely do this. Use Sysmon to see process command lines. If I see Mimikatz or similar tools, or unusual access to LSASS, that's credential theft in progress. Immediate response: isolate system, reset passwords for all accounts that could be compromised."

---

**Q: "How would you identify a DLL hijacking attack?"**

My Answer (Now):
"Use Sysmon to look for Image Load events showing DLLs loaded from unusual locations. Windows normally loads DLLs from System32. If I see a process loading DLL from C:\temp, C:\users, or other non-standard locations, that's suspicious. Cross-reference with file creation events—did someone create a fake DLL in that location? If yes, that's DLL hijacking. Response: delete malicious DLL, scan for others, monitor for similar patterns."

---

## 🚀 CONNECTION TO FUTURE MODULES

**Module 3 prepares me for:**
- **Module 4: Threat Hunting with Elastic** → Use Kibana to hunt Windows logs
- **Module 5: Splunk Fundamentals** → Query Splunk SIEM with Windows events
- **Module 14: Detecting Windows Attacks with Splunk** → Advanced Windows detection

**Module 3 builds on:**
- **Module 2: SIEM** → Understands how logs flow into SIEM
- **Module 1: Incident Response** → Can now investigate incidents with forensic evidence

---

## 📈 PROGRESS SUMMARY

**3/15 Modules Complete (20%)**

| Module | Status | Key Skill |
|--------|--------|-----------|
| 1. Incident Response | ✅ | How to respond |
| 2. SIEM Fundamentals | ✅ | How to detect |
| 3. Windows Event Logs | ✅ | Where evidence comes from |
| 4-15. Advanced Topics | ⏳ | Specialized skills |

**Confidence Progression:**
- After Module 1: 40% confident (know procedures)
- After Module 2: 75% confident (know detection)
- After Module 3: 85% confident (know investigation)
- Target by end of all 15: 95%+ confident

---

## 🏆 FINAL ASSESSMENT

**Module 3 Completion: ✅ SUCCESS**

**Rating:** ⭐⭐⭐⭐⭐ (5.0/5 stars)

**Why 5 stars:**
- Practical forensic knowledge
- Real-world attack examples
- Applicable to daily SOC work
- Directly supports threat detection

**Career Impact:**
- Essential for SOC Analyst role
- Differentiates from candidates without log analysis skills
- Prepares for advanced forensics/incident response roles

**Readiness Assessment:**
- Entry-level SOC Analyst: 85% ready
- With remaining modules: Will be 95%+ ready

---

## 📝 SUMMARY STATEMENT

Module 3: Windows Event Logs & Finding Evil taught me the **forensic foundation** of cybersecurity investigations. I learned that every attack leaves traces in Windows logs. Sysmon captures forensic detail. Get-WinEvent lets me hunt through events efficiently.

Most importantly, I learned that **detection and investigation are connected**. Module 2 taught me SIEM detects threats. Module 3 taught me HOW those detections work—by analyzing Windows Event Logs.

I'm now able to:
✅ Read and interpret Windows Event Logs
✅ Identify malicious event patterns
✅ Use Sysmon for forensic investigation
✅ Query logs with PowerShell
✅ Timeline attacks accurately
✅ Hunt for persistence mechanisms

**Assessment: ✅ READY FOR ADVANCED SOC MODULES**

---

<img width="620" height="397" alt="image" src="https://github.com/user-attachments/assets/57bb4b4b-0bba-427d-85dc-160e9e7d7ead" />

---

<img width="1002" height="692" alt="image" src="https://github.com/user-attachments/assets/b8f5a8d7-9089-4d97-b1b0-172b3e24df60" />


**Module 3 Complete!** 🎉  
**Progress: 3/15 Modules (20%)** 📈  
**Next: Module 4 - Threat Hunting with Elastic** 🔍
