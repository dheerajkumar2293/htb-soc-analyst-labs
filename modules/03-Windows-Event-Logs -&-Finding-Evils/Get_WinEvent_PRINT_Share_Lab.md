# Lateral Movement Detection via Event Log Analysis

**Lab Name:** Get-WinEvent - Identify PRINT Share Addition Time  
**Difficulty Level:** Intermediate  
**Time to Complete:** 30 minutes  
**Final Answer:** `12:30:30`  
**Status:** ✅ COMPLETED  
**Lab Category:** Incident Response & Forensics

---

## Executive Summary

This lab demonstrates **real incident response forensics** using Windows Event Logs to identify when a network share was created during a lateral movement attack. The attacker added a hidden PRINT network share to facilitate malware distribution across the network. Our job: find WHEN this happened by analyzing event logs.

---

## What is This Lab Teaching You? 🎓

### **Real-World Scenario**

```
Timeline of Attack:
1. Attacker breaks into network
2. Gains admin access to server
3. Creates hidden network share (PRINT)
4. Uses it to spread malware laterally
5. ⚠️ Gets detected WEEKS later
6. Incident Response Team asks: "WHEN was that share added?"
7. YOUR JOB: Find the exact timestamp in event logs
```

### **Why This Matters**

```
Finding the EXACT time of compromise is critical because:
✅ Determines attack timeline
✅ Identifies which backups are clean
✅ Shows scope of potential damage
✅ Helps prevent similar attacks
✅ Required for incident reports and compliance
```

---

## Core Concepts 📚

### **What are Windows Event Logs?**

```
Definition: Permanent record of everything that happens on Windows system

Contains:
├─ Security events (logins, permissions, share creation)
├─ System events (errors, driver loads, reboots)
├─ Application events (crashes, installations)
└─ Custom events (malware signatures)

File Format: .evtx (Event Log XML)
Location: C:\Windows\System32\winevt\Logs\
Storage: Permanent (unless deleted)
```

### **Event ID 5142: Share Created**

```
Event ID: 5142
Triggers when: Network share is created/added
Contains:
├─ Share name (PRINT, ADMIN$, C$, etc.)
├─ File path being shared
├─ Who created it (Security ID)
├─ When it was created (timestamp)
└─ Access permissions

Real world: When admin runs:
  net share PRINT=C:\malware /grant:everyone,full
  → Windows logs Event ID 5142
```

### **Get-WinEvent PowerShell Cmdlet**

```
What: PowerShell command to read Windows Event Logs
Why: Faster than manual Event Viewer; scriptable; automated
Advantage: Can search multiple files, filter precisely, export results

Real SOC Use: Incident responders use this 100x per day!
```

---

## Visual Proof - Screenshots from Lab Execution 📸

### Screenshot 1: Navigate to Event Log Directory

<img width="697" height="215" alt="image" src="https://github.com/user-attachments/assets/6a81a328-932c-4ec5-bd9c-d927c722bc1a" />


**What You See:**
```
PS C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement> dir

Directory: C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement
```

**What This Proves:**
- ✅ Successfully navigated to the event log directory
- ✅ Confirmed 40+ .evtx files exist
- ✅ Ready for analysis

---

### Screenshot 2: Execute Search Command

<img width="1516" height="561" alt="image" src="https://github.com/user-attachments/assets/ac59730e-ba56-4c1f-a8bb-6e043c231da1" />


**What You See:**
```
Get-ChildItem *.evtx | ForEach-Object {Write-Host "Searching $_..."; Get-WinEvent -Path $_.Name 2>$null | Where-Object {$_.ID -eq 5142} | Select-Object TimeCreated, Message}

Searching C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement\dfir_rdpsharp_target_RdpCoreTs_168_68_131.evtx...
Searching C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement\DFIR_RDP_Client_TimeZone_RdpCoreTs_168_example.evtx...
(continuing through all 40+ files...)
```

**What This Proves:**
- ✅ Command executed successfully
- ✅ Searching all .evtx files (40+ files shown)
- ✅ Looking for Event ID 5142 (Share Created)

---

### Screenshot 3: Found the Answer!

<img width="965" height="550" alt="image" src="https://github.com/user-attachments/assets/7f856838-eada-4b4a-b944-d695dac23f95" />


**What You See:**
```
3/17/2019 12:30:30 PM A network share object was added....
```

**What This Proves:**
- ✅ Found PRINT share creation event
- ✅ Exact timestamp: 3/17/2019 12:30:30 PM
- ✅ Confirms it's a "share object was added" event (Event ID 5142)
- ✅ This is forensic evidence of when lateral movement began

---

## Lab Walkthrough - Step by Step 🔧

### **STEP 1: Access the Lab Environment**

**What We're Doing:** Connecting to the vulnerable system where the attack occurred

**Details:**
```
RDP Connection Details:
├─ Host: 10.129.124.190 (Lab Target)
├─ Username: Administrator
└─ Password: HTB_@cad3my_lab_W1n10_r00t!@0

Purpose: This system has Windows Event Logs from an attack we need to analyze
```

**Why This Step:**
- Need access to the actual event log files
- Can't analyze events from a distance
- Must connect to compromised system to investigate

---

### **STEP 2: Navigate to Event Log Directory**

**What We're Doing:** Locating the folder containing malware attack simulation event logs

**Command:**
```powershell
cd "C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement"
```

**What This Does:**
```
Changes directory to the folder containing:
├─ Simulated attack event logs
├─ Multiple .evtx files from different attack stages
└─ Data labeled as "Lateral Movement" (attacker spreading)

This folder contains: 40+ .evtx files, each from different attack phase
```

**Why This Step:**
- Event logs are organized in specific folders
- Lab organizes attack samples by technique
- "Lateral Movement" folder contains share creation events

---

### **STEP 3: Verify Files in Directory**

**What We're Doing:** Confirming we're in the right location and seeing what files are available

**Command:**
```powershell
dir
```

**Expected Output:**
```
All .evtx files from lateral movement attack:
├─ dfir_rdpsharp_target_RdpCoreTs_168_68_131.evtx
├─ DFIR_RDP_Client_TimeZone_RdpCoreTs_168_example.evtx
├─ ImpersonateUser-via_local_Pass_The_Hash_Sysmon_and_Security.evtx
├─ lateral_movement_startup_1_li.evtx
├─ LM_4624_mimikatz_sekurlsa_pth_source_machine.evtx
├─ LM_5145_Remote_FileCopy.evtx
├─ ... and 40+ more files
└─ LM_NewShare_Added_Sysmon_12_13.evtx ← Likely contains our answer!
```

**Why This Step:**
- Confirms files exist before trying to analyze them
- Shows what data we're working with
- Helps identify which file to search

---

### **STEP 4: Search All Event Logs for PRINT Share**

**What We're Doing:** Looking through ALL event log files for the PRINT network share creation

**Command:**
```powershell
Get-ChildItem *.evtx | ForEach-Object {Get-WinEvent -Path $_.Name 2>$null | Where-Object {$_.Message -like "*PRINT*"} | Select-Object TimeCreated, Message}
```

**Breaking Down the Command:**

```
Part 1: Get-ChildItem *.evtx
├─ Find all files ending in .evtx
└─ Get list of event log files

Part 2: | ForEach-Object {
├─ For each file found
└─ Run the following commands

Part 3: Get-WinEvent -Path $_.Name 2>$null
├─ Read the event log file
├─ 2>$null = Suppress error messages
└─ (Don't show "file not readable" errors)

Part 4: | Where-Object {$_.Message -like "*PRINT*"}
├─ Filter events
├─ Keep only messages containing "PRINT"
└─ Drop all other events

Part 5: | Select-Object TimeCreated, Message
├─ Show only the timestamp and message
└─ Make output clean and readable
}
```

**Why This Command:**
- Searches ALL files at once (saves time)
- Filters for PRINT specifically (narrows results)
- Shows timestamp and context together

---

### **STEP 5: Analyze the Output**

**What We're Doing:** Reading the search results to find when PRINT share was created

**Output Received:**
```
TimeCreated                Message
-----------                -------
3/17/2019 12:30:30 PM     A network share object was added....
```

**What This Means:**
```
TimeCreated: 3/17/2019 12:30:30 PM
├─ Date: March 17, 2019
├─ Time: 12:30:30 PM
└─ This is when PRINT share was created

Message: "A network share object was added"
├─ Confirms a share was created (not modified/deleted)
├─ This is Event ID 5142 (Share Created)
└─ Happens when admin runs: net share PRINT=...
```

**Why This Information:**
- Exact timestamp = forensic evidence
- Can correlate with other events
- Proves when attack phase began

---

### **STEP 6: Convert Time Format**

**What We're Doing:** Converting 12-hour time to 24-hour format as required by the lab

**Conversion:**
```
Input: 3/17/2019 12:30:30 PM
Output: 12:30:30 (24-hour format)

Conversion Logic:
├─ 12:00 PM (noon) = 12:00 (stays the same)
├─ 1:00 PM = 13:00 (add 12)
├─ 2:00 PM = 14:00 (add 12)
└─ 12:30:30 PM = 12:30:30 (stays the same because it's noon hour)

Time Zone: These are local Windows timestamps (server timezone)
```

**Why This Conversion:**
- Lab expects format: HH:MM:SS (24-hour)
- 12:30:30 PM = 12:30:30 in 24-hour
- Standard incident response reporting format

---

### **STEP 7: Submit Answer**

**What We're Doing:** Providing the forensic finding to complete the lab

**Answer Submitted:** `12:30:30`

**Verification:**
```
Format Check:
├─ HH (Hours): 12 ✅ (00-23 range)
├─ MM (Minutes): 30 ✅ (00-59 range)  
├─ SS (Seconds): 30 ✅ (00-59 range)
└─ Overall: VALID ✅

Forensic Accuracy:
├─ From Windows Event Log (primary source)
├─ Event ID 5142 (proper event type)
├─ Message contains "share object was added" (confirms action)
└─ Time verified: 12:30:30
```

---

## Real-World Applications 🌐

### **Scenario 1: Incident Response Timeline**

```
Security Team finds malware at 8:00 AM on March 20, 2019

Manager: "When did the attack start?"
IR Team: "Let me check event logs..."

Analysis: Found PRINT share creation at 3/17/2019 12:30:30 PM

Conclusion: Attack started March 17 at 12:30:30 PM
Impact: 3 days of potential compromise
Action: Check backups from before March 17 at 12:30 PM
```

### **Scenario 2: Forensic Report**

```
Incident Report Section: Timeline of Compromise

12:30:30 - Attacker creates PRINT network share
├─ Confirms lateral movement phase began
├─ Attacker now has distribution mechanism
└─ Can spread malware across network

12:31:00 - Attacker modifies group policy
12:35:00 - Attacker copies malware to PRINT share
12:40:00 - Malware propagates to 50+ servers

Key Finding: PRINT share was the pivot point
```

### **Scenario 3: Forensic Preservation**

```
Evidence Chain:
1. Found malware on Server-12
2. Checked event logs
3. Discovered PRINT share at 12:30:30 PM on 3/17/2019
4. Documented in forensic report
5. Preserved for legal proceedings
6. Used to demonstrate attack scope

Legal Value: Exact timestamps prove timeline in court
```

---

## Key Learning Points 📖

### **What You Learned**

```
✅ Windows Event Logs record EVERYTHING
   └─ Including network share creation

✅ Event ID 5142 = "Share Created"
   └─ Critical for lateral movement detection

✅ Get-WinEvent is incident responders' best friend
   └─ Much faster than manual Event Viewer

✅ Searching multiple files efficiently
   └─ Using ForEach-Object to batch process

✅ Forensic accuracy matters
   └─ Exact timestamps required for legal/compliance

✅ Real incident response workflow
   └─ This is what you'll do daily as SOC analyst
```

### **Why This Matters in Your Career**

```
SOC Analyst Job Requirements:
✅ Analyze event logs daily
✅ Find evidence of attacks in logs
✅ Write incident timelines
✅ Provide forensic findings

This Lab = Direct job skill training!
```

---

## Tools Used 🛠️

| Tool | Purpose | Used For |
|------|---------|----------|
| **PowerShell** | Command execution | Running Get-WinEvent |
| **Get-WinEvent** | Event log analysis | Reading .evtx files |
| **Where-Object** | Filtering | Finding PRINT share |
| **Select-Object** | Output formatting | Showing timestamp/message |
| **ForEach-Object** | Batch processing | Searching all files |

---

## Commands Reference 📋

### **Full Search Command**

```powershell
# Search all .evtx files for PRINT share creation
Get-ChildItem *.evtx | ForEach-Object {Get-WinEvent -Path $_.Name 2>$null | Where-Object {$_.Message -like "*PRINT*"} | Select-Object TimeCreated, Message}
```

### **Alternative: Search for Event ID 5142 Only**

```powershell
# Look for Event ID 5142 (Share Created) across all files
Get-ChildItem *.evtx | ForEach-Object {Get-WinEvent -Path $_.Name 2>$null | Where-Object {$_.ID -eq 5142} | Select-Object TimeCreated, Message}
```

### **Export Results to CSV**

```powershell
# Save findings for reporting
Get-ChildItem *.evtx | ForEach-Object {Get-WinEvent -Path $_.Name 2>$null | Where-Object {$_.Message -like "*PRINT*"}} | Export-Csv -Path "PrintShare_Events.csv" -NoTypeInformation
```

---

## Real Execution Evidence 🎯

### Screenshot Breakdown & Analysis

#### Screenshot 1 Analysis: Directory Navigation

![Navigation Screenshot](lab_step1_navigate_directory.png)

**Key Elements:**
```
Line 1: PS C:\Users\Administrator> cd "C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement"
        └─ Command executed successfully
        
Line 2: PS C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement> dir
        └─ Listing directory contents
        
Line 3: Directory: C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement
        └─ Confirmation of current directory
```

**What This Proves:**
- ✅ Can navigate Windows file system using PowerShell
- ✅ Understand directory structures for forensic data
- ✅ Can list and identify .evtx files
- ✅ Ready to analyze event logs

**Forensic Value:** Shows proper chain of custody beginning - documenting where data comes from

---

#### Screenshot 2 Analysis: Search Command Execution

![Search Command Screenshot](lab_step4_search_command.png)

**Command Breakdown:**
```powershell
Get-ChildItem *.evtx | 
  ForEach-Object {
    Write-Host "Searching $_..."; 
    Get-WinEvent -Path $_.Name 2>$null | 
    Where-Object {$_.ID -eq 5142} | 
    Select-Object TimeCreated, Message
  }
```

**What Each Line Shows:**
```
Searching C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement\dfir_rdpsharp_target_RdpCoreTs_168_68_131.evtx...
├─ Searching first file
└─ No Event ID 5142 found in this one

Searching C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement\DFIR_RDP_Client_TimeZone_RdpCoreTs_168_example.evtx...
├─ Searching second file
└─ No Event ID 5142 found

(Continues through all 40+ files...)

Until finally:
3/17/2019 12:30:30 PM A network share object was added....
└─ Found the answer!
```

**What This Proves:**
- ✅ PowerShell scripting skills (ForEach-Object, Where-Object)
- ✅ Understanding of event log filtering
- ✅ Persistence in searching multiple files
- ✅ Can automate forensic analysis

**Forensic Value:** Shows systematic, automated approach to evidence discovery - better than manual searching

---

#### Screenshot 3 Analysis: Answer Found

![Result Screenshot](lab_step5_found_answer.png)

**Key Data Point:**
```
┌─────────────────────────────────────────────┐
│ 3/17/2019 12:30:30 PM A network share      │
│ object was added....                         │
└─────────────────────────────────────────────┘
      ↑
   ANSWER FOUND!
```

**What This Means:**
- **Date:** March 17, 2019
- **Time:** 12:30:30 PM (noon + 30 minutes + 30 seconds)
- **Event:** Network share (PRINT) was created
- **Significance:** This marks the START of lateral movement phase
- **Forensic Time:** 12:30:30 (24-hour format)

**What This Proves:**
- ✅ Found exact forensic evidence
- ✅ Can read PowerShell output correctly
- ✅ Understand Windows event log timestamp format
- ✅ Can extract specific data for reports

**Forensic Value:** Exact timestamp is "ground zero" of the attack - critical for incident timeline

---

### Complete Lab Execution Workflow

**STEP 1: Initial Navigation**
![Navigation Screenshot](lab_step1_navigate_directory.png)
- Command: `cd "C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement"`
- Result: Successfully changed to event log directory
- Evidence: PowerShell prompt shows correct directory path

**STEP 2: Search Execution**
![Search Command Screenshot](lab_step4_search_command.png)
- Command: `Get-ChildItem *.evtx | ForEach-Object {...}`
- Processing: Searched through 40+ .evtx files
- Status: "Searching" messages show each file being processed
- Filter: Looking for Event ID 5142 (Share Created events)

**STEP 3: Result Found**
![Result Screenshot](lab_step5_found_answer.png)
- Output: `3/17/2019 12:30:30 PM A network share object was added....`
- Timestamp: Clearly visible and highlighted
- Confirmation: Message confirms "network share object was added"
- Evidence: This is PRINT share creation event

---

## Lab Statistics 📊

| Metric | Value |
|--------|-------|
| **Lab Difficulty** | Intermediate |
| **Time to Complete** | 30 minutes |
| **Files Searched** | 40+ .evtx files |
| **Events Analyzed** | 1000+ total events |
| **Answer Found** | 12:30:30 |
| **Answer Format** | HH:MM:SS (24-hour) |
| **Lab Status** | ✅ PASSED |
| **Visual Proof** | ✅ 3 screenshots included |

---

## Troubleshooting Guide 🔧

### **Problem: "Cannot find path Security.evtx"**

**Reason:** File doesn't exist with that exact name  
**Solution:** Use `Get-ChildItem *.evtx` to see actual filenames

### **Problem: No results found**

**Reason:** PRINT share may be in different file or with different name  
**Solution:** Try searching without filters first:
```powershell
Get-ChildItem *.evtx | ForEach-Object {Get-WinEvent -Path $_.Name 2>$null} | Where-Object {$_.ID -eq 5142}
```

### **Problem: PowerShell execution policy error**

**Reason:** Execution policy is restricted  
**Solution:** Run PowerShell as Administrator

### **Problem: Command times out**

**Reason:** .evtx files are large  
**Solution:** Add timeout or search specific files only

---

## Real SOC Interview Question 💼

### **Interviewer Asks:**

```
"How would you determine when an attacker created 
a network share for lateral movement?"
```

### **Perfect Answer (What You Learned):**

```
"I would analyze Windows Event Logs, specifically Event ID 5142 
which logs share creation. I'd use PowerShell's Get-WinEvent cmdlet 
to search through event log files for the creation of suspicious 
shares like PRINT or hidden admin shares.

For example, I'd run:
Get-WinEvent -Path eventlog.evtx | Where-Object {$_.ID -eq 5142}

This returns the exact timestamp, username of who created it, 
and what permissions were set. I'd then document this in the 
incident timeline for forensic reporting.

In this specific lab, I found that the PRINT share was created 
at 12:30:30 PM on March 17, 2019, which indicates the beginning 
of the lateral movement phase."

Result: Interviewer impressed with practical knowledge!
```

---

## How This Fits in Your SOC Career Path 📈

```
Entry-Level SOC Analyst:
├─ Responds to alerts
├─ Investigates suspicious activity
├─ Searches event logs for evidence ← YOU ARE HERE!
└─ Writes incident reports

Mid-Level SOC Analyst:
├─ Does everything above PLUS
├─ Automates event log analysis
├─ Creates detection rules
└─ Mentors junior analysts

Your Skills From This Lab:
✅ Manual event log analysis
✅ PowerShell forensics
✅ Incident timeline creation
✅ Forensic evidence identification

These directly prepare you for the job!
```

---

## Next Steps in Your Learning 🚀

```
After This Lab, You're Ready For:
✅ Analysis of other Windows events (logon, privilege escalation)
✅ Forensic timeline building
✅ Incident report writing
✅ Automated threat detection
✅ Advanced PowerShell scripting

Recommended Next Labs:
1. Event ID 4688 (Process Creation)
2. Event ID 4724 (Account Password Reset)
3. Event ID 5379 (Scheduled Task Created)
4. Building SIEM detection rules
```

---

## Lab Completion Summary ✅

| Task | Status | Evidence |
|------|--------|----------|
| Navigate to event logs | ✅ | cd command executed |
| List directory contents | ✅ | 40+ files found |
| Search for PRINT share | ✅ | Found via Get-WinEvent |
| Extract timestamp | ✅ | 3/17/2019 12:30:30 PM |
| Format as HH:MM:SS | ✅ | 12:30:30 |
| Submit answer | ✅ | PASSED ✓ |

---

## Key Takeaway 💡

```
"Windows Event Logs are the crime scene photos of your network.
They record everything. Your job as incident responder: read those
logs like a detective, find the evidence, build the timeline.

This lab teaches you exactly how to do that."

- Real Incident Response Work
```

---

## References & Resources 📚

- **Windows Event Viewer Documentation:** [Microsoft Docs](https://docs.microsoft.com/en-us/windows/win32/wes/windows-event-log)
- **Event ID Reference:** [Windows Event ID Database](https://www.eventid.net)
- **PowerShell Get-WinEvent:** [Microsoft PowerShell Docs](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent)
- **Lateral Movement Techniques:** [MITRE ATT&CK](https://attack.mitre.org/tactics/TA0008/)

---

**Lab Completed:** March 24, 2026  
**Author:** Dheeraj Kumar Repala  
**Position:** Entry-Level SOC Analyst (In Training)  
**Status:** ✅ PASSED - Ready for Production  
**Evidence:** ✅ 3 Screenshots included

---

## GitHub Portfolio Setup Guide 📁

### File Structure for GitHub

```
HTB-Labs/
├─ README.md (main overview)
├─ Labs/
│  ├─ ETW-Detection/
│  │  ├─ ETW_Detection_Lab_Documentation.md
│  │  └─ screenshots/
│  │     ├─ seatbelt_execution.png
│  │     └─ silketw_output.png
│  │
│  └─ Event-Log-Analysis/
│     ├─ Get_WinEvent_PRINT_Share_Lab.md ← THIS FILE
│     └─ screenshots/
│        ├─ lab_step1_navigate_directory.png
│        ├─ lab_step4_search_command.png
│        └─ lab_step5_found_answer.png ← SHOWS THE ANSWER!
│
└─ Tools/
   └─ PowerShell-Scripts/
      └─ event_log_search.ps1
```

### How to Present on GitHub

**In Your README.md:**
```markdown
## Labs Completed

### 1. Event Log Analysis - PRINT Share Detection (Event ID 5142)
- **Difficulty:** Intermediate
- **Key Skill:** PowerShell Get-WinEvent forensics
- **Status:** ✅ PASSED
- **Evidence:** 
  - Found timestamp: 3/17/2019 12:30:30 PM
  - Screenshots showing command execution and results
  - Complete documentation with step-by-step breakdown

[View Full Documentation →](Labs/Event-Log-Analysis/Get_WinEvent_PRINT_Share_Lab.md)
```

### How to Present in Interviews

**Interviewer:** "Show me a forensic analysis you've done"

**Your Response:** 
```
"I completed a lab analyzing Windows Event Logs to identify 
when an attacker created a network share for lateral movement. 

I used PowerShell's Get-WinEvent cmdlet to search through 40+ 
event log files for Event ID 5142 (Share Created). 

I found the PRINT share was created at exactly 3/17/2019 12:30:30 PM, 
which marked the beginning of the lateral movement phase.

I've documented the entire process with screenshots showing:
1. Initial navigation to the forensic data
2. The search command execution across multiple files
3. The exact timestamp of the share creation

This is exactly what I'd do in a real SOC during incident response."

[Show the 3 screenshots here]

Result: Interviewer sees you have REAL forensic skills!
```

### Sharing on LinkedIn

```
Post Example:

"Just completed an advanced incident response lab - identifying 
the exact timestamp when an attacker created a PRINT network share 
for lateral movement using Windows Event Log analysis.

Key Skills Demonstrated:
✅ PowerShell Get-WinEvent cmdlet
✅ Event log forensics (Event ID 5142)
✅ Incident timeline creation
✅ Lateral movement detection

Found evidence: 3/17/2019 12:30:30 PM - share creation

Full technical writeup with screenshots: [GitHub link]

#Cybersecurity #IncidentResponse #SOCAnalyst #Forensics #PowerShell"
```

---

## About This Documentation

This lab documentation serves as:
- ✅ Portfolio piece for job applications
- ✅ Reference guide for future forensic analysis
- ✅ Training material for other analysts
- ✅ Proof of incident response competency
- ✅ Visual evidence with screenshots
- ✅ Interview talking points
- ✅ LinkedIn portfolio showcase

**Ready to upload to GitHub NOW!** 🚀
