# Module 2: Security Monitoring & SIEM Fundamentals

**Status:** ✅ COMPLETED  
**Date Completed:** August 2026  
**Difficulty:** Easy (Tier 2)  
**Time Investment:** ~5 hours  
**Points Earned:** 20 Cubes  
**Rating:** ⭐⭐⭐⭐⭐ (5.0/5)

---

## 📚 Module Overview

This module bridges the gap between **incident response theory** (Module 1) and **practical detection** by teaching you how SIEM platforms actually work. You learned the frameworks; now you learn the tools that make detection possible.

**Key Insight:** Module 1 taught WHAT to do when incidents happen. Module 2 teaches HOW to detect them using SIEM tools.

---

## 🎯 Module Sections Completed (4 Total)

### ✅ Section 1: SIEM & SOC Fundamentals

**What I Learned:**

#### SIEM Definition & Fundamentals (Theory)
- **SIEM = Security Information and Event Management**
- Collects logs from all organizational systems
- Correlates data to detect threats
- Creates alerts for suspicious activity
- Provides dashboards and reporting

**Key SIEM Components:**
1. **Data Collection** — Gather logs from servers, firewalls, applications
2. **Aggregation** — Combine logs from multiple sources
3. **Correlation** — Find relationships between events
4. **Alerting** — Notify analysts of threats
5. **Visualization** — Dashboards showing security posture
6. **Reporting** — Compliance and incident reports

**Why SIEM Matters in SOC:**
- SOC analysts spend ~60-70% of time in SIEM
- SIEM is the central nervous system of security operations
- Good SIEM tuning = fewer false positives = better work
- Bad SIEM setup = alert fatigue = missed real threats

---

#### Introduction To The Elastic Stack (Interactive)
- **Elastic Stack (ELK) = Popular open-source SIEM**

**Components:**
1. **Beats** — Lightweight agents collecting logs (Filebeat, Metricbeat, Packetbeat)
2. **Logstash** — Processing and enriching log data
3. **Elasticsearch** — Storing and indexing logs (searchable database)
4. **Kibana** — Visualization and dashboarding interface

**How It Works Together:**
```
Servers/Apps → Beats → Logstash → Elasticsearch → Kibana
(Collect)     (Ship)   (Process)  (Store)        (View)
```

**Comparison to Splunk:**
- Splunk = Commercial, more features, expensive
- Elastic = Open-source, free tier, growing market
- Both used heavily in enterprise SOC

---

#### SOC Definition & Fundamentals (Theory)
- **SOC = Security Operations Center**
- Team of analysts monitoring and responding to threats
- Works 24/7 (shifts or outsourced)
- Center of security defense operations

**SOC Roles:**
1. **Tier 1 Analyst** — Alert triage, initial investigation
2. **Tier 2 Analyst** — Deep investigation, escalation
3. **Tier 3 Specialist** — Expert investigation, complex incidents
4. **SOC Manager** — Leadership and coordination
5. **Incident Response Team** — Handle major incidents

**SOC Workflow:**
```
SIEM Alert → Tier 1 Review → Real Threat? → Tier 2 Investigate → Response
                ↓                             ↓
             False +                    Escalate/Resolve
```

**You Will Be:** Tier 1 SOC Analyst (entry-level)

---

#### MITRE ATT&CK & Security Operations (Theory)
- **MITRE ATT&CK Framework = Security Industry Standard**
- Catalog of attacker tactics and techniques
- Used for detection, threat intelligence, and SOC maturity

**ATT&CK Structure:**
1. **Tactics** — WHAT attacker is trying to do
   - Example: "Lateral Movement" = spread through network

2. **Techniques** — HOW they do it
   - Example: "Admin Shares" = accessing admin shares for lateral movement

3. **Procedures** — Specific implementation
   - Example: "Use PsExec to access \\admin$"

**Why SOC Uses ATT&CK:**
- Map detection rules to ATT&CK techniques
- Identify gaps in monitoring
- Understand attack lifecycle
- Communicate with management using common language

**Real Example:**
- Attacker logs in with stolen credential (Tactic: Initial Access)
- Uses this access to run malware (Tactic: Execution)
- Installs backdoor (Tactic: Persistence)
- Moves laterally to other systems (Tactic: Lateral Movement)
- Steals data (Tactic: Exfiltration)

SOC job: Detect at any of these stages!

---

#### SIEM Use Case Development (Theory)
- **Use Case = A specific detection rule**
- Example: "Alert when 10+ failed logins in 5 minutes"

**Developing a Use Case:**
1. **Define the threat** — What are we looking for?
2. **Identify the data** — What logs contain this?
3. **Build the query** — How do we search for it?
4. **Test the rule** — Does it work? False positives?
5. **Deploy** — Turn it on in production
6. **Tune** — Adjust based on real results

**Real Use Case Example:**

**Threat:** Brute force attack on user accounts

**Data needed:** Authentication logs showing failed login attempts

**Query (Splunk SPL):**
```
index=auth sourcetype=auth failed_login=true
| stats count as failed_logins by user, src_ip
| where failed_logins > 10
```

**Alert:** If 10+ failures from same IP in 5 minutes → Alert SOC

**Response:** Block IP, reset user password, check for successful login

---

### ✅ Section 2: SIEM Visualization Development (Completed)

**What I Learned:**

Dashboards are HOW SOC analysts see threats. Bad dashboards = missed threats.

#### SIEM Visualization Example 1: Failed Logon Attempts (All Users)
- **Purpose:** Detect brute force attacks
- **Visualization Type:** Graph or table
- **Shows:** Failed login count over time by source IP
- **Alert Trigger:** Sudden spike in failures
- **Response:** Block the IP, investigate user accounts

**What This Protects:** User accounts from being compromised

---

#### SIEM Visualization Example 2: Failed Logon Attempts (Disabled Users)
- **Purpose:** Detect attacks on dormant accounts
- **Key Insight:** Attackers scan for any account, even disabled ones
- **Why Important:** Disabled accounts should have ZERO login attempts
- **Alert Trigger:** Any failed login on disabled account
- **Response:** 100% red flag - investigate immediately

**What This Protects:** Reveals attackers probing your systems

---

#### SIEM Visualization Example 3: Successful RDP Logon (Service Accounts)
- **Purpose:** Detect stolen service account credentials
- **Key Insight:** Service accounts should only login from specific systems
- **Why Important:** Service accounts have high privileges
- **Alert Trigger:** Service account login from unusual location
- **Response:** Rotate credentials, investigate access

**What This Protects:** High-privilege accounts from compromise

---

#### SIEM Visualization Example 4: Users Added/Removed From Local Groups
- **Purpose:** Detect privilege escalation
- **Key Insight:** Attackers add accounts to admin groups to maintain access
- **Why Important:** Local admin = complete system access
- **Alert Trigger:** Any unauthorized group membership change
- **Response:** Remove unauthorized users, investigate

**What This Protects:** System access control from being bypassed

---

### ✅ Section 3: Alert Triaging (Completed)

**What I Learned:**

Alert triaging is your MAIN JOB as a Tier 1 SOC analyst.

#### The Triaging Process (Theory)

**Step 1: Receive Alert**
```
SIEM sends alert → "Suspicious Activity Detected"
```

**Step 2: Review Alert Details**
- What triggered the alert?
- Which user/system?
- When did it happen?
- What was the action?

**Step 3: Investigate**
- Look at context
- Check other logs
- Is this normal for this user?
- Have I seen this before?

**Step 4: Classify**
```
Real Threat? → Escalate to Tier 2
            ↓
False Positive? → Close alert, tune rule to reduce noise
            ↓
Suspicious but Unknown? → Flag for review
```

**Step 5: Document**
- Record your findings
- Note what you checked
- Explain your decision
- Help team learn

---

### ✅ Section 4: Skills Assessment (Completed)

**Assessment Result:** ✅ PASSED

**Topics Tested:**
- ✓ SIEM architecture and components
- ✓ Elastic Stack data flow
- ✓ SOC definition and roles
- ✓ MITRE ATT&CK framework application
- ✓ Use case development
- ✓ Alert triaging process
- ✓ Visualization best practices

**Key Questions Mastered:**

1. **"What is a SIEM?"**
   - Tool that collects, correlates, and visualizes security data from entire organization

2. **"What are SIEM components?"**
   - Collection → Aggregation → Correlation → Alerting → Visualization

3. **"How does Elastic Stack work?"**
   - Beats collect → Logstash processes → Elasticsearch stores → Kibana displays

4. **"What's a SOC?"**
   - Team of analysts monitoring and responding to threats 24/7

5. **"How do you use MITRE ATT&CK?"**
   - Map attacks to framework for detection and understanding

6. **"What's a use case?"**
   - Specific detection rule targeting a threat

7. **"What's alert triaging?"**
   - Determining if alert is real threat or false positive

---

## 🗺️ Mapping to MITRE ATT&CK

This module teaches detection for these tactics:

- **Initial Access** — Detecting first entry (failed logins = brute force detection)
- **Execution** — Detecting malware execution (process monitoring)
- **Persistence** — Detecting backdoor installation (account creation, scheduled tasks)
- **Privilege Escalation** — Detecting privilege increase (group membership changes)
- **Defense Evasion** — Detecting hiding techniques
- **Lateral Movement** — Detecting network traversal (admin share access)
- **Collection** — Detecting data gathering
- **Exfiltration** — Detecting data theft
- **Command & Control** — Detecting attacker communication

**Connection:** Each MITRE tactic has detection opportunities. SIEM finds them.

---

## 💡 Key Takeaways (What I'll Use in My SOC Job)

### 1. **SIEM is Your Workspace**
- I'll spend most of my time in SIEM dashboards
- I need to understand how logs flow through the system
- Good SIEM tuning is critical

### 2. **Elastic Stack is Common**
- Elastic is used in many SOCs
- Understanding architecture helps troubleshooting
- Kibana is similar to Splunk's interface

### 3. **SOC Has Tiers**
- I'll start as Tier 1 (triage and initial investigation)
- Can grow to Tier 2 or 3 with experience
- Understanding each tier's role helps teamwork

### 4. **MITRE ATT&CK is Universal**
- Every SOC uses it
- Every detection rule maps to ATT&CK
- Helps me understand threat landscape

### 5. **Use Cases Drive Detection**
- Each rule protects against specific threats
- Good rules catch real attacks without false positives
- Tuning is ongoing work

### 6. **Triaging is Science & Art**
- Requires knowledge (knowing what's normal)
- Requires judgment (recognizing context)
- Gets easier with experience

### 7. **Context Matters**
- Same action can be normal or malicious depending on context
- Good analyst understands the business
- False positives destroy team productivity

---

## 📊 Comparison: Module 1 vs Module 2

| Aspect | Module 1 | Module 2 |
|--------|----------|----------|
| **Focus** | What to do when incident happens | How to detect incidents |
| **Topic** | Incident Response Process | SIEM & SOC Operations |
| **Learning** | Framework and procedures | Tools and detection |
| **Practical** | Understanding processes | Using SIEM dashboards |
| **Career Stage** | Before incident starts | Detecting incident |
| **Next Step** | Needs tools to detect | Build use cases |

**Together:** 
- Module 1 = Response playbook
- Module 2 = Detection capability
- Combined = Complete SOC competency

---

## 🔗 Connected Modules

**This module connects to:**
- **Module 3: Windows Event Logs** → Where SIEM gets data from
- **Module 4: Threat Hunting with Elastic** → Using Elastic for proactive searching
- **Module 5: Log Sources & Splunk** → Different SIEM, similar concepts
- **Module 14: Detecting Windows Attacks with Splunk** → Advanced Splunk detection

---

## 🎓 Interview-Ready Answers You Can Give

1. **"What is a SIEM and why does a SOC need one?"**
   - SIEM collects logs from entire organization, correlates data, creates alerts. SOC uses it to detect threats 24/7.

2. **"How does the Elastic Stack work?"**
   - Beats collect data → Logstash processes it → Elasticsearch indexes it → Kibana visualizes it

3. **"What's the difference between Splunk and Elastic?"**
   - Splunk = commercial, more features, expensive. Elastic = open-source, free tier, growing adoption. Both detect threats.

4. **"How do you detect a brute force attack?"**
   - Alert on 10+ failed logins in 5 minutes from same source IP. Block the IP and reset user password.

5. **"What's your approach to alert triaging?"**
   - Review alert details → Investigate context → Classify as real/false positive → Document findings

6. **"How do you prevent alert fatigue?"**
   - Tune rules to reduce false positives → Validate alerts before escalating → Use baselines for normal behavior

7. **"How does this connect to MITRE ATT&CK?"**
   - Each detection rule maps to ATT&CK techniques. Helps SOC understand attack lifecycle and coverage gaps.

---

## ✨ Real-World Application

**Scenario:** You're on Tier 1 SOC

**Alert comes in:** "Failed Login Attempts Detected"

**Your job (now that you completed Module 2):**
1. Open SIEM dashboard
2. Review the alert details
3. Check if this is normal for that user
4. Decide: Real threat or false positive?
5. Document your finding
6. If real: Escalate to Tier 2
7. If false positive: Update rule to reduce noise

**Before Module 2:** You wouldn't understand SIEM well enough  
**After Module 2:** You know exactly how to use SIEM to investigate  

---

## 📈 Your Progress

```
Module 1: Incident Handling Process  ✅ COMPLETE
Module 2: Security Monitoring & SIEM ✅ COMPLETE (NEW!)

Progress: 2/15 modules (13.3%)

Next: Module 3 - Windows Event Logs & Finding Evil
```

**What This Means:**
- ✅ You understand incident response
- ✅ You understand how detection works
- ✅ You're building complete SOC knowledge
- ⏳ Now learn WHERE the data comes from (Windows logs)

---

## 💼 Value for Your Resume

**You can now say:**
- ✅ "I understand SIEM architecture and data flow"
- ✅ "I can explain Elastic Stack and Splunk"
- ✅ "I know alert triaging process"
- ✅ "I can map threats to MITRE ATT&CK"
- ✅ "I can develop detection use cases"

**Recruiters will see:**
- You've completed 2 foundational modules
- You're building hands-on SIEM knowledge
- You understand how SOC actually works
- You're serious about the role

---

## 📝 Module Summary

| Aspect | Details |
|--------|---------|
| **Time Invested** | ~5 hours |
| **Difficulty** | Easy (Tier 2) |
| **Topics** | SIEM, Elastic, SOC, MITRE ATT&CK, Use Cases, Triage |
| **Sections** | 4 sections (11 total interactive) |
| **Cubes Earned** | 20 |
| **Rating** | 5 stars ⭐⭐⭐⭐⭐ |
| **Assessment** | ✅ PASSED |

---

## 🎉 What's Next?

**Module 3: Windows Event Logs & Finding Evil**
- Learn where SIEM gets data from
- Understand Windows security events
- Find malicious activity in Windows logs
- Apply detection to Windows systems

**Why This Matters:**
- 90%+ of enterprise runs Windows
- Windows events are primary detection source
- Completing this makes you job-ready for many SOCs

---

## 🏆 Confidence Level

**Before this module:** 40% confident in SIEM/SOC concepts  
**After this module:** 75% confident in SIEM/SOC operations  
**Will reach 95%+** after Module 3 (Windows logs)

---

## 📸 Screenshots

*After adding to GitHub, include:*
- Module completion screen
- Skills assessment passed
- Section breakdown (all 4 complete)
- Rating (5 stars)

---

## ⏱️ Time Breakdown

- SIEM & SOC Fundamentals: 1.5 hours
- Elastic Stack Interactive: 0.5 hours  
- SIEM Visualization Development: 1.5 hours
- Alert Triaging Process: 0.75 hours
- Skills Assessment: 0.75 hours
- **Total: ~5 hours**

---

**Module 2 Complete! ✅**  
Ready to move to **Module 3: Windows Event Logs & Finding Evil**
