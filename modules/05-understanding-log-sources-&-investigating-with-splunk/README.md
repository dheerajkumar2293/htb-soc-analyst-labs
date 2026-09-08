# Understanding Log Sources & Investigating with Splunk - Complete Module Documentation

## Module Overview

**Module Name:** Understanding Log Sources & Investigating with...  
**Difficulty:** Medium  
**Tier:** Tier 2  
**Estimated Duration:** 8 hours  
**Points Earned:** 20 Cubes  
**Status:** ✅ COMPLETED

---

## Executive Summary

This foundational module taught **essential SIEM skills** for investigating security incidents using Splunk. Through hands-on labs, I learned how to identify log sources, understand event codes, construct effective searches, and correlate data to detect and investigate threats.

**Key Achievement:** This module provided the foundation for all subsequent labs (intrusion detection, lateral movement, anomaly detection).

---

## What I Learned 🎓

### 1. Splunk Fundamentals

#### Architecture Understanding
```
Data Flow:
Forwarders (UF/HF) → Indexers → Search Heads → Interface

Components:
- Forwarders: Collect data from sources
- Indexers: Process and store data
- Search Heads: Query interface (what we use)
- Apps/Add-ons: Splunk extensions
```

#### Index Structure
```
index="main" = Default storage location for logs
Other indices: _internal, _audit, security_logs

Data retention: Configurable (7 days to years)
Storage: Indexed data for fast searching
```

#### Search Processing Language (SPL)
```
SPL = Query language for Splunk
Pipes (|) = Chain commands together
Pattern: base_search | command1 | command2 | ...
```

---

### 2. Log Sources & Event Types

#### Windows Event Logs
```
Sourcetype: WinEventLog:<LogType>

Key Event Logs:
- Security: Authentication, privilege escalation
- System: OS-level events
- Application: Application errors and events
- PowerShell: PowerShell script execution
```

#### Sysmon (System Monitor)
```
Sourcetype: WinEventLog:Sysmon
Better than built-in Windows logging

Event Codes:
- EventCode=1: Process Creation
- EventCode=3: Network Connection
- EventCode=5: Process Terminated
- EventCode=7: Image/DLL Loaded
- EventCode=8: CreateRemoteThread (code injection)
- EventCode=10: ProcessAccess (accessing another process)
- EventCode=11: FileCreate
- EventCode=13: RegistryEvent
- EventCode=22: DNSEvent

Why Sysmon?
✅ More detailed than Windows logs
✅ Better for threat hunting
✅ Captures process relationships
✅ Industry standard for SIEM
```

#### DNS Logs
```
Sourcetype: dns
Shows: DNS queries and responses
Usage: Find C2 communication, data exfiltration
```

#### Network Logs
```
Sourcetype: firewall, proxy, network
Shows: Connection attempts, traffic patterns
Usage: Lateral movement, external communication
```

---

### 3. Event Code Deep Dive

#### EventCode=1 (Process Creation)
```
When: A new process is created
Shows: Image, CommandLine, ParentImage, User
Usage: Detect process execution, suspicious tools

Example:
cmd.exe started → EventCode=1
Shows: Who ran it, what command, parent process
```

#### EventCode=3 (Network Connection)
```
When: Network connection established
Shows: Source/Dest IP, Port, Process
Usage: Detect C2, lateral movement, data exfiltration

Example:
Process connects to 10.0.0.50:443 → EventCode=3
Shows: Which process, where it connected, when
```

#### EventCode=8 (CreateRemoteThread)
```
When: One process injects thread into another
Shows: SourceImage, TargetImage, ThreadID
Usage: Detect code injection, malware activity

Example:
rundll32.exe creates thread in explorer.exe → EventCode=8
Shows: Attacker process, victim process
```

#### EventCode=10 (ProcessAccess)
```
When: One process opens handle to another
Shows: SourceImage, TargetImage, GrantedAccess
Usage: Detect credential dumping (lsass access)

Example:
notepad.exe accesses lsass.exe → EventCode=10
Shows: Credential theft attempt
```

---

### 4. Basic SPL Query Construction

#### Simple Search
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1
```

**Breaking it down:**
- `index="main"` → Search in main index
- `sourcetype="WinEventLog:Sysmon"` → Only Sysmon logs
- `EventCode=1` → Only process creation events

#### Adding Filters
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 
Image="*cmd.exe"
```

**What this does:**
- Find all cmd.exe process creation events
- Filters by Image field
- Shows when cmd.exe was executed

#### Using Wildcards
```
* = Match anything
?cmd.exe → matches acmd.exe, bcmd.exe, cmd.exe
*cmd.exe → matches anything ending in cmd.exe
cmd*.exe → matches cmd.exe, cmdline.exe, cmdexe.exe
```

#### Field Selection
```spl
index="main" EventCode=1 Image="*cmd.exe"
| fields Image, CommandLine, ParentImage, User
```

**What this does:**
- Shows only the specified fields
- Easier to read results
- Filters out unnecessary data

---

### 5. Advanced Query Techniques

#### Stats Command (Aggregation)
```spl
| stats count by Image
```

**Shows:** How many times each process ran

#### Table Command (Display)
```spl
| table Image, CommandLine, User
```

**Shows:** Results in organized table format

#### Sort Command
```spl
| sort - count
```

**Shows:** Results sorted by count (descending)

#### Dedup Command (Remove Duplicates)
```spl
| dedup Image
```

**Shows:** Each unique process only once

#### Where Command (Filter)
```spl
| where count > 10
```

**Shows:** Only results where count exceeds 10

#### Eval Command (Calculate)
```spl
| eval ratio = count / total
```

**Shows:** Create new calculated fields

---

### 6. Investigation Methodology

#### 5-Step Investigation Process

**Step 1: Define the Question**
```
What are we looking for?
- Specific process? User? Event type?
- Time range? Specific system?
```

**Step 2: Identify Data Source**
```
Which logs contain what we need?
- Process execution → EventCode=1
- Network → EventCode=3
- DLL loading → EventCode=7
```

**Step 3: Construct Search**
```
Build query targeting that data
Start broad, then refine
Test at each stage
```

**Step 4: Analyze Results**
```
Look for:
- Anomalies (unusual values)
- Patterns (repeated behaviors)
- Relationships (process chains)
```

**Step 5: Correlate Data**
```
Connect pieces together:
- Parent/child processes
- Timeline of events
- Affected systems
```

---

## Lab Process & What I Did 🔬

### Phase 1: Learning Splunk Fundamentals

**Activity 1: Index Exploration**
```spl
index="main" | stats count by sourcetype
```

**What I discovered:**
- How many log sources available
- Which sourcetypes have data
- Data distribution

**Activity 2: Event Code Discovery**
```spl
index="main" sourcetype="WinEventLog:Sysmon" | stats count by EventCode
```

**What I discovered:**
- Which EventCodes are common
- Which have relevant data
- What events are available

---

### Phase 2: Basic Investigations

**Investigation 1: Process Execution Analysis**
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 Image="*cmd.exe"
| stats count by ComputerName
```

**What I learned:**
- How to find process execution
- Which systems ran specific tools
- Frequency analysis

**Investigation 2: Network Connection Analysis**
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=3
| stats count by DestinationIp, DestinationPort
| sort - count
```

**What I learned:**
- How to identify network traffic
- Finding C2 communication patterns
- Port analysis

---

### Phase 3: Advanced Investigations

**Investigation 3: DLL Loading Analysis**
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=7 
Image="*rundll32*"
| stats count by ImageLoaded
```

**What I learned:**
- How to track DLL loading
- Identifying suspicious DLL patterns
- Process behavior analysis

**Investigation 4: Process Access Analysis**
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=10
TargetImage="*lsass*"
| stats count by SourceImage
```

**What I learned:**
- How to detect credential dumping attempts
- Process access patterns
- Privilege escalation indicators

---

### Phase 4: Correlation & Timeline Building

**Activity 1: Building Process Chains**
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1
| table _time, ParentImage, Image, CommandLine
| sort _time
```

**What I learned:**
- Parent-child process relationships
- Attack timeline construction
- Process spawning patterns

**Activity 2: Identifying Attack Patterns**
```spl
index="main" sourcetype="WinEventLog:Sysmon" 
(EventCode=1 AND Image="*powershell*") 
OR (EventCode=3 AND DestinationPort=443)
| stats count by Image, EventCode
```

**What I learned:**
- Multi-event correlation
- Attack pattern recognition
- Complex query construction

---

## Key Concepts Mastered 🎯

### 1. Log Source Selection
```
For each investigation question:
✅ Know which logs contain the answer
✅ Understand what each EventCode captures
✅ Choose the right sourcetype
✅ Filter appropriately
```

### 2. Query Construction
```
Efficient searching means:
✅ Specific index selection
✅ Precise sourcetype filtering
✅ Targeted EventCode matching
✅ Relevant field selection
```

### 3. Data Interpretation
```
Understanding results means:
✅ Recognizing normal vs anomalous
✅ Identifying patterns
✅ Understanding context
✅ Correlating events
```

### 4. Investigation Methodology
```
Systematic approach:
✅ Define question clearly
✅ Identify relevant data source
✅ Construct targeted search
✅ Analyze results thoroughly
✅ Correlate with other data
```

---

## Connection to Other Labs 🔗

### How This Module Enabled Subsequent Labs

**Lab 1: Intrusion Detection with Splunk**
```
Built on this module:
- EventCode knowledge (EventCode=10 for lsass)
- Query construction (complex multi-stage searches)
- Log source understanding (Sysmon events)
- Investigation methodology (timeline building)

New skills added:
- Advanced aggregation (stats, eventstats)
- MITRE ATT&CK mapping
- Complete attack chain analysis
```

**Lab 2: PsExec Detection**
```
Built on this module:
- Process creation analysis (EventCode=1)
- Registry monitoring (EventCode=13)
- Named pipes (EventCode=17/18)
- Service creation detection

New skills added:
- Service exploitation understanding
- Credential extraction detection
- Lateral movement identification
```

**Lab 3: Anomaly Detection - Thread Injection**
```
Built on this module:
- EventCode=8 (thread injection)
- Process identification
- Source vs Target analysis
- Timeline reconstruction

New skills added:
- Statistical baseline calculation
- Outlier detection (2σ threshold)
- Anomaly-based detection
- Distribution analysis
```

---

## Skills Demonstrated 💼

### Technical Skills
✅ **Splunk Expertise**
- Index navigation
- SPL query construction
- Data filtering and aggregation
- Results analysis

✅ **Log Source Knowledge**
- Sysmon event codes
- Windows Event Logs
- Network logs
- Multi-source correlation

✅ **Investigation Techniques**
- Timeline building
- Pattern recognition
- Process relationship analysis
- Attack chain reconstruction

✅ **Data Analysis**
- Statistical methods
- Anomaly detection
- Baseline comparison
- Threshold setting

### Soft Skills
✅ **Problem Solving**
- Breaking down questions
- Identifying relevant data
- Constructing solutions
- Testing approaches

✅ **Analysis Methodology**
- Systematic approach
- Thorough documentation
- Pattern recognition
- Critical thinking

✅ **Communication**
- Clear explanations
- Technical writing
- Query documentation
- Findings presentation

---

## Interview Talking Points 🎤

> *"I completed the 'Understanding Log Sources & Investigating with Splunk' module, which provided foundational SIEM skills. The module taught me how to navigate Splunk, understand event codes (particularly Sysmon), construct effective SPL queries, and investigate security incidents systematically.*
>
> *Key learnings included understanding the role of different log sources—Sysmon for process execution and code injection, Windows Event Logs for authentication, and network logs for lateral movement. I learned that Sysmon EventCodes like 1 (process creation), 8 (thread injection), and 10 (process access) are critical for threat detection.*
>
> *The investigation methodology taught in this module—defining the question, identifying data sources, constructing searches, analyzing results, and correlating events—became the foundation for all subsequent labs. This systematic approach enabled me to move from basic process execution analysis to complex multi-stage threat investigations.*
>
> *Practically, this module prepared me for advanced threat hunting. Once I understood log sources and basic query construction, I could move to detecting PsExec lateral movement, identifying thread injection attacks, and building statistical baselines for anomaly detection.*
>
> *The module emphasized starting with a clear question, finding the right data source, and constructing targeted searches. This discipline has become core to my approach: always know what question I'm asking, always ensure I have the right log source, and always filter precisely."*

---

## Key Takeaways 📝

### What I Learned
1. **Splunk is about asking questions of data**
   - Know what you're looking for
   - Find the data that contains the answer
   - Filter precisely
   - Analyze thoroughly

2. **Log sources are everything**
   - Different EventCodes tell different stories
   - Sysmon is superior to Windows logs
   - Network logs reveal communication
   - Correlation connects pieces

3. **Investigation is systematic**
   - Define question → Identify source → Construct search → Analyze → Correlate
   - Start broad, refine gradually
   - Build understanding progressively
   - Document findings clearly

4. **SPL is a tool, not magic**
   - Learn the building blocks
   - Understand what each command does
   - Practice combining commands
   - Test iteratively

---

## Lab Completion Journey 📊

```
Module Progress:

Week 1: Splunk Fundamentals
├─ Architecture understanding
├─ Index exploration
├─ Log source identification
└─ Basic search construction

Week 2: Event Code Mastery
├─ EventCode=1 (process creation)
├─ EventCode=3 (network connection)
├─ EventCode=8 (thread injection)
└─ EventCode=10 (process access)

Week 3: Query Construction
├─ Simple filtering
├─ Advanced aggregation
├─ Timeline building
└─ Complex correlation

Week 4: Investigation Methodology
├─ 5-step approach
├─ Pattern recognition
├─ Anomaly identification
└─ Report writing

Result: Foundation for all future labs ✅
```

---

## Application to Real-World Scenarios 🌍

### Scenario 1: Suspicious Process Execution
```
Question: Who ran unusual tools?

Using this module:
1. Check EventCode=1 (process creation)
2. Filter by Image (process name)
3. Look at CommandLine (arguments)
4. Check ParentImage (who started it)
5. Review User (who executed)

Result: Quick identification of suspicious execution
```

### Scenario 2: Network Communication
```
Question: What external systems did we contact?

Using this module:
1. Check EventCode=3 (network connection)
2. Filter by DestinationIp
3. Check DestinationPort
4. Identify SourceImage (which process)
5. Build timeline

Result: C2 detection, data exfiltration identification
```

### Scenario 3: Code Injection Attack
```
Question: Is malware injecting code?

Using this module:
1. Check EventCode=8 (thread creation)
2. Filter by SourceImage (attacker)
3. Check TargetImage (victim)
4. Count threads (frequency)
5. Correlate with EventCode=10 (process access)

Result: Complete code injection detection
```

---

## Preparation for Next Steps 🚀

### What This Module Enabled
✅ Understanding of Splunk architecture  
✅ Knowledge of log sources  
✅ EventCode mastery  
✅ SPL query proficiency  
✅ Investigation methodology  
✅ Foundation for threat detection  

### Ready For
✅ Intrusion detection challenges  
✅ Lateral movement analysis  
✅ Anomaly detection  
✅ Advanced threat hunting  
✅ SOC analyst role  

---

## Summary of Labs Completed 📚

### Complete Learning Path

**Module 1: Understanding Log Sources & Investigating** ✅
- Status: COMPLETED
- Points: 20 Cubes
- Foundation: ESSENTIAL

**Lab 1: Intrusion Detection with Splunk** ✅
- Status: COMPLETED
- Difficulty: Advanced
- 5 challenges solved
- Skills: Advanced detection, multi-stage investigation

**Lab 2: PsExec Lateral Movement Detection** ✅
- Status: COMPLETED
- Difficulty: Intermediate
- Credential extraction, service exploitation
- Skills: Lateral movement detection

**Lab 3: Anomaly Detection - Thread Injection** ✅
- Status: COMPLETED
- Difficulty: Intermediate
- 3 challenges solved
- Skills: Statistical analysis, anomaly detection

---

## Total Progress 📊

```
Learning Portfolio:
- Modules completed: 1
- Labs completed: 3
- Total challenges: 8
- Documentation: 3000+ lines
- SPL queries: 50+
- Skills developed: 35+

Confidence Level: ADVANCED
Ready for: SOC analyst interviews
GitHub Portfolio: PORTFOLIO-READY
```

---

## Conclusion

The "Understanding Log Sources & Investigating with Splunk" module provided **essential foundational knowledge** for all threat detection work. By mastering log sources, understanding EventCodes, and learning systematic investigation methodology, I built a strong foundation that enabled successful completion of three advanced labs.

**This module transformed me from someone who could run searches to someone who understands WHAT to search for, WHERE to search, and HOW to interpret results—the true foundation of threat hunting and incident response.**

---

**Module Completed:** September 2026  
**Status:** ✅ FULLY DOCUMENTED  
**Points Earned:** 20 Cubes  
**Next Steps:** Advanced threat hunting, incident response specialization

---

## Quick Reference Guide 📖

### EventCode Quick Lookup
```
EventCode=1: Process Creation
EventCode=3: Network Connection
EventCode=5: Process Terminated
EventCode=7: DLL/Image Loaded
EventCode=8: Thread Created in Another Process
EventCode=10: Process Opened Handle to Another
EventCode=11: File Created
EventCode=13: Registry Key Modified
EventCode=22: DNS Query
```

### Common Query Patterns
```
Find unusual processes:
| EventCode=1 | stats count by Image | where count > 10

Find network connections:
| EventCode=3 | stats count by DestinationIp

Find DLL loading:
| EventCode=7 | stats count by ImageLoaded

Find process access:
| EventCode=10 | stats count by TargetImage
```

### Investigation Checklist
```
☑ Define the question
☑ Identify log source
☑ Construct query
☑ Analyze results
☑ Correlate events
☑ Build timeline
☑ Document findings
```

<img width="846" height="357" alt="image" src="https://github.com/user-attachments/assets/7fe2849f-2bc6-4431-9cf0-09521aa33a29" />

---

**You've successfully mastered the foundations of SIEM investigation!** 🎓🏆
