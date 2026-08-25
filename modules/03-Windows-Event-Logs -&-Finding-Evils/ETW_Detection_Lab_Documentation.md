# HTB Academy: ETW Detection Lab - Documentation

**Lab Name:** Detecting Malicious .NET Assembly Loading Using ETW  
**Difficulty Level:** Advanced  
**Time to Complete:** 12 hours  
**Final Answer:** `GetTokenInformation`  
**Status:** ✅ COMPLETED

---

## Lab Overview

This lab teaches **detection of malicious .NET assembly loading** using Event Tracing for Windows (ETW). The goal was to:
1. Understand how attackers use BYOL (Bring Your Own Land) strategy
2. Use SilkETW to capture .NET runtime events
3. Analyze ETW data to identify malicious method calls
4. Find a specific `ManagedInteropMethodName` starting with "G" and ending with "ion"

---

## Key Concepts Learned

### 1. **BYOL Attack Strategy**
- **Definition:** Attackers bring their own custom .NET tools instead of using built-in Windows tools
- **Advantage:** Hard to detect because they load in memory only (no files on disk)
- **Example:** Seatbelt reconnaissance tool

### 2. **Seatbelt Tool**
- **Purpose:** Information-gathering tool written in C# (.NET)
- **What it does:** Collects system info, privileges, running processes, security settings
- **How attackers use it:** Recon before escalating privileges or spreading malware
- **Key method:** `GetTokenInformation()` - gathers user token/privilege information

### 3. **ETW (Event Tracing for Windows)**
- **Advantage over Sysmon:** Monitors at kernel level, captures detailed method calls
- **Provider used:** `Microsoft-Windows-DotNETRuntime`
- **Keywords captured:**
  - **JitKeyword:** Code being compiled at runtime
  - **LoaderKeyword:** Assemblies being loaded
  - **InteropKeyword:** Managed-to-unmanaged code calls
  - **NGenKeyword:** Pre-compiled assemblies

### 4. **Parent PID Spoofing (Bonus Concept)**
- Attackers fake parent-child process relationships
- Sysmon can be fooled by spoofed PPID values
- ETW provides more reliable detection

---

## Lab Steps & Troubleshooting

### **Step 1: Setup SilkETW (The Spy Camera)**

**Command:**
```cmd
cd C:\Tools\SilkETW_SilkService_v8\v8\SILKETW

SilkETW.exe -t user -pn Microsoft-Windows-DotNETRuntime -uk 0x2038 -ot file -p C:\windows\temp\etw.json
```

**Purpose:** Start capturing .NET runtime events to a JSON file

**Challenges:**
- Initially had wrong path (SilkETW.exe not found)
- Solution: Verified folder structure with `dir` commands

**Expected Output:**
```
[+] Collector parameter validation success..
[+> Starting trace collector (Ctrl-c to stop)..
```

---

### **Step 2: Run Seatbelt (Trigger Malware Activity)**

**Command (in separate Command Prompt):**
```cmd
cd "C:\Tools\GhostPack Compiled Binaries"

Seatbelt.exe all
```

**Purpose:** Trigger .NET method calls that SilkETW will capture

**Key Point:** While Seatbelt runs, SilkETW records all methods being called in the background

**What Seatbelt Output Shows:**
```
====== TokenPrivileges ======
SeDebugPrivilege:  SE_PRIVILEGE_ENABLED
SeImpersonatePrivilege:  SE_PRIVILEGE_ENABLED
(etc.)
```

---

### **Step 3: Stop SilkETW (Save Recording)**

**Action:** Press `Ctrl+C` in the first Command Prompt

**Purpose:** Stop recording and save data to C:\windows\temp\etw.json

---

### **Step 4: Analyze the ETW Output**

**Commands Tried:**
```cmd
# Initial attempts (had path issues)
findstr /C:"GetInformation" C:\windows\temp\etw.json
findstr /C:"GetAuthentication" C:\windows\temp\etw.json

# Verification that file exists
dir C:\windows\temp\*.json
```

**Result:** File existed (2,008,641 bytes)

---

### **Step 5: Extract All G...ion Methods**

**Successful PowerShell Command:**
```powershell
(Get-Content C:\windows\temp\etw.json -Raw) -split 'ManagedInteropMethodName' | ForEach-Object { if ($_ -match '":"(G[^"]*ion)"') { $matches[1] } } | Sort-Object -Unique
```

**Result:** Found the answer!

---

## Challenge Faced During Lab

| Challenge | Issue | Resolution |
|-----------|-------|-----------|
| **Path Error** | SilkETW.exe not found | Verified correct folder structure with `dir` |
| **Wrong Search Results** | GetInformation was incorrect | Extracted ALL methods and checked each one |
| **File Path Issues** | Couldn't find etw.json initially | Confirmed file was created, then used correct full path |
| **Search Strategy** | Manual Notepad search was too slow | Switched to PowerShell for automated extraction |
| **Wrong Answer** | GetEventInformation was wrong | Used PowerShell to find ALL methods, identified GetTokenInformation |

---

## Final Answer

**ManagedInteropMethodName:** `GetTokenInformation`

**Why This Answer:**
- ✅ Starts with "G"
- ✅ Ends with "ion"
- ✅ Seatbelt calls this method to gather token/privilege information
- ✅ Part of Windows API interop (unmanaged code calling)

**What It Means:**
When Seatbelt runs, it calls `GetTokenInformation()` to extract details about the current user's security token, including privileges like `SeDebugPrivilege` and `SeImpersonatePrivilege`.

---

## Key Takeaways

### For SOC Analysts:
1. **ETW is powerful:** More detailed than Sysmon for .NET detection
2. **Method names matter:** Identifying what methods malware calls reveals intent
3. **Persistence pays off:** Troubleshooting and trying different approaches leads to answers
4. **Automation helps:** PowerShell scripts can quickly analyze large JSON files
5. **Documentation matters:** Save your findings for future reference

### For Threat Hunting:
1. Monitor for unexpected .NET DLL loading (clr.dll, mscoree.dll)
2. Use ETW to see what methods are actually being called
3. Known malware methods: `GetTokenInformation`, `CreateProcess`, `AdjustPrivilege`
4. Combine Sysmon + ETW for high-confidence detections

### Technical Skills Practiced:
- Command line troubleshooting
- Path navigation in Windows
- ETW tool usage (SilkETW)
- JSON data analysis
- PowerShell scripting
- Security tool output interpretation

---

## Lab Statistics

| Metric | Value |
|--------|-------|
| **Time Spent** | 12 hours |
| **Challenges Faced** | 5 major issues |
| **Commands Executed** | 30+ |
| **File Size Analyzed** | 2.0 MB (etw.json) |
| **Methods Extracted** | 1000+ events |
| **Answer Found** | GetTokenInformation |
| **Attempts** | 3 (GetInformation, GetEventInformation, GetTokenInformation) |

---

## Related Labs Completed

- ✅ Q1: DLL Hijacking Detection (Hash: 51F2305DCF385056C68F7CCF5B1B3B9304865CEF1257947D4AD6EF5FAD2E3B13)
- ✅ Q2: Unmanaged PowerShell Injection (Hash: 8A3CD3CF2249E9971806B15C75A892E6A44CCA5FF5EA5CA89FDA951CD2C09AA9)
- ✅ Q3: Credential Dumping (Hash: 5e4ffd54b3849aa720ed39f50185e533)
- ✅ ETW Detection Q1: .NET Assembly Loading (Answer: **GetTokenInformation**)

---

## Tools Used

| Tool | Purpose | Version |
|------|---------|---------|
| **SilkETW** | ETW event capture | v8 |
| **Seatbelt** | .NET reconnaissance tool | v1.2.1 |
| **Event Viewer** | Windows event analysis | Built-in |
| **Notepad** | JSON viewing | Built-in |
| **PowerShell** | JSON parsing & analysis | 5.1+ |
| **Command Prompt** | File operations | Windows 10 |

---

## Resources & Commands Reference

### ETW Monitoring
```powershell
# Capture .NET runtime events
SilkETW.exe -t user -pn Microsoft-Windows-DotNETRuntime -uk 0x2038 -ot file -p C:\windows\temp\etw.json

# Extract specific methods from output
(Get-Content C:\windows\temp\etw.json -Raw) -split 'ManagedInteropMethodName' | ForEach-Object { if ($_ -match '":"(G[^"]*ion)"') { $matches[1] } } | Sort-Object -Unique
```

### Detection Keywords
- **0x2038 in binary:** JIT (0x10) + Interop (0x20) + Loader (0x08) + NGen (0x2000)
- Monitor for: Seatbelt, CobaltStrike, Mimikatz .NET variants

---

## Conclusion

This lab demonstrates the power of ETW for detecting advanced threats that standard Sysmon monitoring might miss. By understanding how attackers use BYOL strategies with .NET, and by leveraging ETW's kernel-level visibility, SOC analysts can effectively detect and respond to sophisticated attacks.

**Key Learning:** Persistence, proper troubleshooting methodology, and automation tools are essential for modern threat detection.

---

**Completed:** August 24, 2026  
**Author:** Dheeraj Kumar Repala  
**Status:** ✅ PASSED
