# 🛡️ Wazuh HIDS/HIPS Lab – Threat Detection & Anti-Forensics Analysis

## 📌 Scenario
As a cybersecurity professional, this project demonstrates how to use **Wazuh (HIDS/HIPS + SIEM)** to detect and analyze **Indicators of Compromise (IoCs)**.

The lab simulates:
- Password brute-force attacks (RDP)
- Suspicious authentication activity
- Anti-forensics (log deletion)
- Rule-based detection using Wazuh IDS

---

## 🎯 Objectives

- Detect brute-force login attempts using Wazuh
- Analyze Wazuh alerts and MITRE ATT&CK mapping
- Review IDS rule configurations
- Simulate anti-forensics (log clearing)
- Detect log tampering using Wazuh alerts
- Correlate events with tactics and techniques

---

## 🧱 Lab Environment

| System | Role |
|------|------|
| KALI Linux | Attacker |
| DC10 (Windows Server 2019) | Target |
| WAZUH (Ubuntu Server) | SIEM / HIDS |

---

## 🏗️ Architecture


KALI (Attacker)
↓
DC10 (Windows Logs)
↓
Wazuh Agent → Wazuh Server → SIEM Dashboard


---

# 🔍 Phase 1: Logon Attack Detection (Brute Force)

## 🚀 Step 1: Prepare Password List

```bash
cp /usr/share/seclists/Passwords/500-worst-passwords.txt /home/kali/passlist.txt

Add password at line 17:

Pa55w0rd!
💣 Step 2: Perform RDP Brute Force Attack
hydra -t 1 -V -f -l administrator -P /home/kali/passlist.txt rdp://10.1.16.1 -o creds.txt
📊 Step 3: Analyze Wazuh Alerts
Navigate to Wazuh → Security Events
Filter:
Agent: DC10
Description: RDP
🔎 Key Findings
Rule ID: 92657
Event Source: Windows Security Logs
Event ID captured from logs
Attack detected as suspicious authentication behavior

📌 Wazuh mapped this to MITRE ATT&CK (may be inaccurate due to rule logic)

🧠 MITRE ATT&CK Mapping
Technique: T1550.002
Description: Use of alternate authentication material (detected incorrectly for this attack)

📌 Note:
Actual attack performed = Password Guessing (Brute Force)
Wazuh mapped = Pass-the-Hash (False classification)

⚙️ Phase 2: Wazuh Rule Analysis
🔍 Locate Rule
cd /var/ossec/ruleset/rules
grep 92657 -r .
📊 Rule Breakdown
Rule ID: 92657
Alert Level: (varies based on rule file)
Purpose: Detect suspicious authentication activity

📌 Wazuh rules are XML-based and include:

Rule ID
Severity Level (0–16)
MITRE Mapping
Pattern matching logic
🔐 Phase 3: Logon Event Correlation
🔁 Simulate Share Access
❌ Failed Attempt
mount -t cifs //10.1.16.1/C$ /mnt/dc10-c -o username=administrator,password=letmein
✅ Successful Attempt
mount -t cifs //10.1.16.1/C$ /mnt/dc10-c -o username=administrator,password=Pa55w0rd!
📊 Wazuh Detection
Failure Event ID: 60122
Success Event ID: 60106
🔎 Observations
NTLM authentication detected
Wazuh correlates:
Failed login → suspicious activity
Successful login → potential compromise
🧨 Phase 4: Anti-Forensics Detection
🚀 Step 1: Clear Logs on DC10
Option 1: PowerShell
Clear-EventLog -LogName Security
Clear-EventLog -LogName Application
Clear-EventLog -LogName System
Option 2: Command Prompt
wevtutil cl Security
wevtutil cl Application
wevtutil cl System
📊 Step 2: Detect in Wazuh
Navigate to Security Events

Search:

The audit log was cleared
🚨 Key Detection
Description: The audit log was cleared
Event Type: Anti-forensics
Detection Source: Windows Event Logs

📌 This indicates:

An attacker attempting to erase evidence of activity

🧠 MITRE ATT&CK Mapping
Tactic: Defense Evasion
📊 Key Findings
Brute-force attack successfully detected
Logon failures and successes correlated
Wazuh rules triggered correctly (with minor misclassification)
Anti-forensic activity detected in real-time
SIEM visibility confirmed across all attack phases
🧠 Skills Demonstrated
SIEM Monitoring (Wazuh)
Threat Detection & Analysis
Log Analysis (Windows Event Logs)
MITRE ATT&CK Mapping
Incident Investigation
IDS Rule Analysis
Anti-Forensics Detection
🔐 Security Concepts
HIDS vs HIPS
Indicators of Compromise (IoCs)
Brute Force Attacks
NTLM Authentication Monitoring
Log Integrity & Anti-Forensics
SIEM Correlation
🏁 Summary

✔️ Simulated brute-force attack using Hydra
✔️ Detected suspicious login activity in Wazuh
✔️ Analyzed rule-based detections (Rule ID 92657)
✔️ Correlated authentication events (success/failure)
✔️ Simulated anti-forensics (log deletion)
✔️ Successfully detected log tampering
