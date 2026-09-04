# Intrusion Detection with Splunk - Complete Lab with Screenshots

## Overview

This lab demonstrates **real-world threat hunting and intrusion detection** using Splunk SIEM. The objective was to identify a multi-stage attack involving credential dumping, lateral movement, and command & control (C2) communication across a compromised network.

**Dataset:** 500,000+ Windows Security and Sysmon events  
**Environment:** Multi-machine network (5 systems) with multiple infections

---

## Quick Reference - All Answers

| Challenge | Answer | Evidence |
|-----------|--------|----------|
| **1. Credential Dumping Process** | `rundll32.exe` | ProcessAccess to lsass (EventCode 10) |
| **2. DLL Used for Dumping** | `comsvcs.dll` | Process creation with MiniDump export |
| **3. Execute-Assembly Process** | `rundll32.exe` | Temporary clr.dll loading (count: 4) |
| **4. C2 Server IPs** | `10.0.0.91` and `10.0.0.186` | Network connections with high frequency |
| **5. C2 Port** | `3389` | RDP protocol abuse for C2 |

---

## Challenge 1: Credential Dumping Detection 🔐

### Objective
Find which process (other than notepad.exe) dumped the lsass.exe credentials.

### Methodology

**Step 1: Explore Available Data Sources**

First, I checked what data sources were available in the environment:

```spl
index="main" | stats count by sourcetype
```

<img width="1090" height="368" alt="image" src="https://github.com/user-attachments/assets/34ccbe3f-25a0-439a-a015-89e765441729" />

This revealed Windows Event Logs and Sysmon as key data sources.

**Step 2: Search for Sysmon Events**

Then I targeted Sysmon logs for computer name "uniwaldo.local":

```spl
index="main" sourcetype="WinEventLog:Sysmon" ComputerName="uniwaldo.local"
```

**Step 3: Find Process Access Events**

I searched for EventCode 10 (ProcessAccess) events that accessed lsass:

```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=10 lsass
| stats count by SourceImage
```

<img width="1090" height="337" alt="image" src="https://github.com/user-attachments/assets/1ff17929-bc1a-464e-99a9-4d80bbfd2f11" />

**Step 4: Analyze Results**

<img width="1090" height="467" alt="image" src="https://github.com/user-attachments/assets/8d7e7559-f291-4693-a745-09fa7f5738da" />

Results showed:
- **lsass.exe**: 99 occurrences (accessing itself - normal)
- **notepad.exe**: Multiple suspicious accesses (already identified)
- **rundll32.exe**: Few suspicious accesses (the answer!)

### Key Insight
**rundll32.exe** is commonly weaponized because:
- Legitimate Windows utility (appears normal to defenders)
- Can load and execute malicious DLLs
- Low frequency = temporary execution
- Typical living-off-the-land attack

### Answer: `rundll32.exe` ✅

---

## Challenge 2: Malware DLL Identification 📦

### Objective
Find which DLL was used by rundll32.exe to dump lsass credentials.

### Methodology

**Step 1: Analyze Source Images for rundll32**

<img width="1090" height="388" alt="image" src="https://github.com/user-attachments/assets/2572065a-a884-4fdc-bd0a-455650ca48dc" />

I needed to find what DLL rundll32 loaded. The screenshot shows multiple source images being analyzed.

**Step 2: Search for Process Creation Events**

I searched for EventCode 1 (Process Creation) to see the command-line arguments:

```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 Image="*rundll32*"
```

<img width="1090" height="262" alt="image" src="https://github.com/user-attachments/assets/b840416c-a154-4e86-9585-0568bc6952ab" />

This revealed the DLL being loaded by rundll32.

### Key Finding: comsvcs.dll

The command-line showed:
```
rundll32.exe comsvcs.dll MiniDump
```

**Why comsvcs.dll?**
- Part of Windows Component Object Model
- Contains MiniDump export (dumps process memory)
- Legitimate Windows file (hard to detect as malware)
- Perfect for extracting credentials from lsass

### Answer: `comsvcs.dll` ✅

---

## Challenge 3: C# Code Injection Detection 💻

### Objective
Detect suspicious clr.dll loads (execute-assembly attacks) and identify which process was used for temporary code execution.

### Methodology

**Step 1: Search for CLR.DLL Loads**

I searched for EventCode 7 (ImageLoaded) with clr.dll:

```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=7 clr.dll
| stats count by Image
```

<img width="1090" height="389" alt="image" src="https://github.com/user-attachments/assets/702df99f-cde9-4927-86f9-90872d57916c" />

Found multiple processes loading clr.dll. Now needed to identify suspicious ones.

**Step 2: Filter Out Legitimate Processes**

```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=7 clr.dll 
| where Image!="*Program Files*" 
| where Image!="*Microsoft.NET*"
| where Image!="*msiexec*"
| where Image!="*Teams*"
| stats count by Image
| where count < 10
```

<img width="1090" height="404" alt="image" src="https://github.com/user-attachments/assets/dd5a2076-6283-44ca-988b-ad89fdfe7209" />

**Step 3: Identify Temporary Execution**

<img width="1090" height="225" alt="image" src="https://github.com/user-attachments/assets/5c9aae03-2fe9-4267-b195-53be11ea0eeb" />

Results showed:
- **notepad.exe**: 12 clr.dll loads (persistent)
- **rundll32.exe**: 4 clr.dll loads (TEMPORARY!)

### Key Insight: Temporary Execution Pattern

```
rundll32 spawned → loads clr.dll → executes C# code → terminates
Low count (4) = brief execution = shellcode/temporary code
```

This is the classic "execute-assembly" attack pattern used by Cobalt Strike and similar frameworks.

### Answer: `rundll32.exe` ✅

---

## Challenge 4: C2 Server Detection 🎯

### Objective
Identify two C2 (Command & Control) callback server IP addresses used to control compromised machines.

### Methodology

**Step 1: Discover Network Connections**

I initially searched without filtering:

```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=3 ComputerName="uniwaldo.local"
```

<img width="1090" height="354" alt="image" src="https://github.com/user-attachments/assets/d383581c-ae71-4512-ba83-96c61cf102e1" />

Noticed many connections with a count around 10, then refined to focus on destination IPs.

**Step 2: Aggregate by Destination IP**

```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=3 DestinationIp="10.0.0.*"
| stats count by DestinationIp
| sort - count
```

<img width="1090" height="241" alt="image" src="https://github.com/user-attachments/assets/b0e367da-995c-482d-b208-4991d3f7b799" />

Identified **10.0.0.91** with 143 connections as the primary C2 server.

**Step 3: Find Secondary C2 Server**

Looking for another server in the 10.0.0.1XX range:

```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=3 DestinationIp="10.0.0.1*"
| stats count by DestinationIp, ComputerName
```

<img width="1090" height="295" alt="image" src="https://github.com/user-attachments/assets/1a7e1b09-90f7-458b-a0c0-93712eb61fcf" />

Found candidates: 10.0.0.137 (12 connections) and 10.0.0.186 (7 connections)

### Critical Discovery: Multiple Machines = C2!

The key insight: **10.0.0.186** had connections from **3 different compromised machines**, confirming C2 coordination:

```
10.0.0.137: 12 connections from 1 machine (likely normal traffic)
10.0.0.186: 7 connections from 3 machines (C2 command server!)
```

**C2 Pattern:**
- Multiple machines connect to same IP
- Indicates coordination between compromised systems
- Typical of botnet or C2 infrastructure

### Answer: `10.0.0.91` and `10.0.0.186` ✅

---

## Challenge 5: C2 Port Detection 🔌

### Objective
Identify the port used by the C2 servers to communicate with compromised machines.

### Methodology

**Step 1: Find Connections to C2 Servers**

```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=3 
(DestinationIp="10.0.0.91" OR DestinationIp="10.0.0.186") 
| stats count by DestinationIp, DestinationPort, ComputerName
| sort - count
```

**Step 2: Analyze Port Usage**

<img width="1090" height="315" alt="image" src="https://github.com/user-attachments/assets/0db0f3ba-26cd-46b0-87e7-890884d32e5c" />

Results showed various ports being used for communication.

**Step 3: Identify Primary C2 Port**

<img width="1090" height="376" alt="image" src="https://github.com/user-attachments/assets/067ae1b7-0e1c-454a-a645-9c89be09cbc2" />

Port analysis revealed:
- **Port 3389** = RDP (Remote Desktop Protocol)
- Used by multiple compromised machines
- Perfect for C2 because RDP traffic is encrypted and often allowed

### Why Port 3389?

RDP abuse for C2 is common because:
- Legitimate Windows service (remote administration)
- Often allowed through firewalls
- Encrypted channel (HTTPS-like)
- Can carry encoded C2 commands
- Blends in with legitimate traffic

### Answer: `3389` ✅

---

## Full Attack Timeline & Kill Chain 🔗

```
┌─────────────────────────────────────────────────────────────┐
│               INTRUSION KILL CHAIN                           │
└─────────────────────────────────────────────────────────────┘

STAGE 1: INITIAL COMPROMISE
├─ Weaponized notepad.exe & rundll32.exe
├─ Gained code execution on target machines
└─ Foothold established

STAGE 2: CREDENTIAL THEFT (Challenge 1-2)
├─ rundll32.exe loaded comsvcs.dll
├─ Used MiniDump export to dump lsass.exe memory
├─ Extracted all user credentials from memory
├─ Obtained domain user accounts & hashes
└─ PRIVILEGE ESCALATION: Access to domain accounts

STAGE 3: IN-MEMORY CODE EXECUTION (Challenge 3)
├─ rundll32.exe loaded clr.dll
├─ Executed C# code via execute-assembly attack
├─ Temporary process execution (count: 4)
├─ Likely Cobalt Strike beacon deployment
└─ PERSISTENCE: Advanced C2 agent installed

STAGE 4: COMMAND & CONTROL SETUP (Challenge 4)
├─ Established C2 connection to 10.0.0.91
├─ Backup C2 connection to 10.0.0.186
├─ Multiple compromised machines beaconing
└─ COMMAND & CONTROL: Full network compromise

STAGE 5: C2 COMMUNICATION (Challenge 5)
├─ Used port 3389 (RDP) for C2 channel
├─ Encrypted traffic blends with legitimate RDP
├─ Ongoing malicious command execution
└─ FULL NETWORK COMPROMISE: Attacker has complete control
```

---

## MITRE ATT&CK Mapping 🎯

| Attack Stage | MITRE ATT&CK Technique | Evidence |
|--------------|------------------------|----------|
| Execution | T1204.002 - User Execution: Malicious File | notepad.exe weaponization |
| Credential Access | T1110 - Brute Force / Credential Dumping | lsass dumping via rundll32 + comsvcs.dll |
| Execution | T1059.008 - Command Line Interface: PowerShell | Execute-assembly via clr.dll |
| Command & Control | T1071.001 - Application Layer Protocol: Web Protocols | C2 over HTTP/HTTPS ports |
| Command & Control | T1021.006 - Remote Services: RDP | C2 over port 3389 (RDP) |

---

## SPL Query Reference 📋

### Process Forensics

**Find suspicious process access to lsass:**
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=10 lsass 
| stats count by SourceImage
```

**Find process creation with CommandLine:**
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 Image="*rundll32*" 
| table CommandLine, SourceUser, ComputerName
```

### Malware Detection

**Find suspicious DLL loading:**
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=7 clr.dll 
| where Image!="*Program Files*" 
| where Image!="*Microsoft.NET*"
| stats count by Image
```

**Filter out false positives:**
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=7 clr.dll 
| where Image!="*Program Files*" 
| where Image!="*Microsoft.NET*"
| where Image!="*msiexec*"
| where Image!="*Teams*"
| where count < 10
```

### Network Detection

**Find all connections to internal subnet:**
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=3 DestinationIp="10.0.0.*" 
| stats count by DestinationIp, DestinationPort
| sort - count
```

**Identify C2 pattern (multiple machines to same IP):**
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=3 DestinationIp="10.0.0.91" 
| stats count by ComputerName, DestinationPort
```

---

## Key Forensic Indicators 🔍

### Red Flags for Credential Dumping
- ✅ Unusual process accessing lsass.exe
- ✅ Low frequency = temporary execution
- ✅ Command-line with MiniDump or secretsdump
- ✅ Process running under SYSTEM user
- ✅ rundll32.exe + comsvcs.dll combination

### Red Flags for Code Injection
- ✅ Suspicious clr.dll loading
- ✅ Low event count = temporary execution
- ✅ Non-development process loading .NET framework
- ✅ UNKNOWN memory regions in CallTrace
- ✅ Process termination soon after execution

### Red Flags for C2 Communication
- ✅ Single IP receiving connections from multiple machines
- ✅ Consistent high-frequency beaconing
- ✅ Unusual ports for normal traffic (3389 for C2)
- ✅ Regular network connections to external IPs
- ✅ Encrypted tunneling (HTTPS/RDP) misuse

---

## Lessons Learned 🎓

### 1. Know Your Event Codes
```
EventCode 1  = Process Creation (execution forensics)
EventCode 3  = Network Connection (C2 detection)
EventCode 7  = Image Loaded (code injection detection)
EventCode 10 = ProcessAccess (credential theft detection)
```

### 2. Follow the Attack Chain
Most attacks follow a predictable sequence:
```
Execution → Persistence → Privilege Escalation → 
Credential Theft → Lateral Movement → C2
```

### 3. Aggregate Smartly
```
Single machine + high count   = likely normal behavior
Multiple machines + same dest = coordination = C2
```

### 4. Weaponized Legitimate Tools
Attackers abuse built-in Windows tools because:
- Already exist on systems (no malware detection)
- Often allowed by firewalls/AV
- Appear legitimate in logs
- Examples: rundll32, powershell, notepad, cmd

### 5. Temporary Execution = Suspicious
- Low event counts (4 vs 1000)
- Quick process lifecycle
- Process spawned then terminated
- Often indicates shellcode/temporary code

---

## Real-World Impact 💼

**This attack represents actual threat campaigns:**
- Credential dumping → Initial privilege escalation
- Execute-assembly → In-memory code execution (Cobalt Strike)
- C2 communication → Command & control over legitimate protocols
- Port 3389 → Frequently abused for hidden C2 channels

**Detection Strategy for Defenders:**
1. Monitor ProcessAccess (EventCode 10) targeting lsass
2. Alert on suspicious DLL loads (EventCode 7: clr.dll, ntdll.dll)
3. Detect network beaconing patterns (EventCode 3: repeated connections)
4. Correlate multiple machines to same destination IP
5. Investigate temporary process execution patterns

---

## Skills Demonstrated 💪

✅ **Advanced SPL Query Writing**
- Multi-stage queries with filtering
- Aggregation and statistics
- Pattern recognition

✅ **Malware Forensics**
- Credential dumping detection
- Process behavior analysis
- DLL loading patterns

✅ **Code Injection Detection**
- Execute-assembly attacks
- Temporary code execution patterns
- Memory-based execution indicators

✅ **Network-Based Threat Hunting**
- C2 server identification
- Beaconing pattern detection
- Port abuse recognition

✅ **Incident Investigation Methodology**
- Data source exploration
- Threat actor behavior analysis
- Attack chain reconstruction

---

## Interview Talking Points 🎤

> *"I completed an advanced Splunk threat hunting lab where I analyzed 500,000+ security events to identify a multi-stage intrusion. Here's what I discovered and how I detected it:*
>
> *First, I found credential dumping via rundll32.exe using the comsvcs.dll DLL - this leveraged the legitimate MiniDump export to extract passwords from lsass memory. EventCode 10 (ProcessAccess) logs were key.*
>
> *Next, I detected code injection through suspicious clr.dll loading - a technique called execute-assembly used by Cobalt Strike. The temporary execution pattern (only 4 events) was a red flag.*
>
> *Finally, I identified two C2 servers: 10.0.0.91 and 10.0.0.186. The critical indicator was multiple compromised machines connecting to the same IP - that coordination is a dead giveaway of C2 infrastructure.*
>
> *The C2 used port 3389 (RDP protocol abuse) to hide malicious commands in legitimate-looking traffic.*
>
> *This demonstrates my ability to conduct real-world incident investigations, perform threat hunting using SIEM data, and reconstruct complete attack chains from log evidence."*

---

## Tools & Technologies Used

| Tool | Purpose | Usage |
|------|---------|-------|
| **Splunk** | SIEM / Log aggregation | Primary analysis platform |
| **Sysmon** | Advanced process monitoring | Process & network events |
| **Windows Event Logs** | OS-level security events | Authentication & system events |
| **SPL (Splunk Query Language)** | Log analysis & hunting | Query construction & filtering |
| **EventCode Analysis** | Event interpretation | Identifying attack indicators |

---

## Portfolio Value 📁

**GitHub Repository Structure:**
```
splunk-siem-labs/
├── README.md
├── intrusion-detection-splunk-complete.md (THIS FILE)
├── intrusion-detection-splunk-lab.md
├── lab-images/
│   ├── image1.png through image13.png
├── spl-queries/
│   ├── challenge1-credential-dumping.spl
│   ├── challenge2-dll-identification.spl
│   ├── challenge3-code-injection.spl
│   ├── challenge4-c2-detection.spl
│   └── challenge5-port-detection.spl
└── attack-chain-diagram.txt
```

---

## Conclusion

This lab demonstrated **end-to-end threat hunting** using SIEM data. By analyzing 500,000+ events, I identified:

✅ Credential dumping process (rundll32.exe)  
✅ Weaponized DLL (comsvcs.dll)  
✅ Code injection attack (execute-assembly/clr.dll)  
✅ Command & control servers (10.0.0.91, 10.0.0.186)  
✅ C2 communication port (3389)  

**This represents the full attack lifecycle and demonstrates critical SOC analyst and threat hunter skills.**

---

## References

- [MITRE ATT&CK - Credential Dumping (T1110)](https://attack.mitre.org/techniques/T1110/)
- [MITRE ATT&CK - Execute-Assembly](https://attack.mitre.org/techniques/T1059/008/)
- [Sysmon Event Codes Documentation](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)
- [Cobalt Strike Documentation](https://www.cobaltstrike.com/)
- [SANS ICS - Threat Hunting](https://www.sans.org/white-papers/threat-hunting/)

---

**Lab Completed:** September 2026  
**Format:** GitHub-Ready with Screenshots  
**Status:** ✅ Ready for Portfolio

---

## Quick Answer Reference (No Spoilers Format)

For quick reference without spoilers:

```
Challenge 1: Find the credential dumping process
Answer: rundll32.exe

Challenge 2: Find the DLL used for dumping
Answer: comsvcs.dll

Challenge 3: Find the execute-assembly process
Answer: rundll32.exe

Challenge 4: Find the two C2 servers
Answer: 10.0.0.91 and 10.0.0.186

Challenge 5: Find the C2 communication port
Answer: 3389
```
