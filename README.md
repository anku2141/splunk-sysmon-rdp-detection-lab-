# splunk-sysmon-rdp-detection-lab-

Adversary simulation using RDP exploitation and malware detection with Splunk &amp; Sysmon
# Adversary Simulation Lab: RDP Exploitation & Detection using Splunk + Sysmon

## Objective
Simulate a real-world cyber attack by exploiting RDP and executing a malicious payload, then analyze logs using Splunk and Sysmon to detect suspicious activity.

---

## Lab Setup
- Attacker Machine: Kali Linux
- Target Machine: Windows
- Monitoring Tool: Splunk Enterprise
- Log Source: Sysmon logs

---

## Attack Simulation

### 1. Reconnaissance

- Performed network scanning using Nmap
- Identified multiple open services on target machine:

### Open Ports Discovered:
- **80/tcp (HTTP - IIS)** → Web service exposed (potential attack surface)
- **3389/tcp (RDP)** → Remote Desktop access enabled (high-risk entry point)
- **5985/tcp (WinRM)** → Windows Remote Management enabled (remote command execution risk)

### Security Insight:
- RDP (3389) provided direct access path for exploitation
- WinRM (5985) could be leveraged for remote command execution or lateral movement
- HTTP (80) indicates additional attack surface for web-based exploitation

### 2. Payload Creation
- Created reverse shell using msfvenom
- Named payload as Resume.pdf.exe

### 3. Listener Setup
- Configured Metasploit multi-handler

### 4. Payload Delivery
- Hosted payload using Python HTTP server
- Downloaded on target machine

### 5. Execution
- Executed payload
- Received reverse shell connection

### 6. Post Exploitation
- Executed commands:
  - net user
  - net localgroup
  - ipconfig

---

## Log Analysis (Splunk + Sysmon)

- Sysmon logs were ingested into Splunk under index=main
- Focused on Event ID 1 (Process Creation) for tracking execution behavior

### Key Observations:
- Suspicious executable `Resume.pdf.exe` was identified
- The process spawned `cmd.exe`, indicating command execution
- Multiple enumeration commands were executed after payload execution

### Process Chain:
Resume.pdf.exe → cmd.exe → system commands (net user, ipconfig)

---

## Detection Queries
- index=main EventCode=1 Image="*Resume.pdf.exe*"
- index=main EventCode=1 ParentImage="*Resume.pdf.exe*"
- index=main EventCode=1 CommandLine="*net user*"
- index=main EventCode=3 dest_port=3389

---

## Detection Logic

- Detect execution of suspicious executables mimicking document names
- Alert when such executables spawn command-line interpreters (cmd.exe)
- Monitor rapid execution of administrative commands
- Identify unusual parent-child process relationships

---
## Incident Summary

### Attack Flow:
1. RDP service discovered on port 3389
2. Malicious payload delivered via HTTP server
3. Payload executed on target system
4. Reverse shell established
5. System enumeration commands executed

### Impact:
- Unauthorized remote access gained
- System-level commands executed
- Potential data exposure risk
  
---
## MITRE ATT&CK Mapping

- T1021.001: Remote Desktop Protocol (RDP)
- T1078: Valid Accounts (RDP login)
- T1105: Ingress Tool Transfer (payload delivery)
- T1059.001: Command and Scripting Interpreter (cmd.exe)
  
---

## Detection Outcome

- Successfully detected malicious payload execution using Sysmon logs
- Identified process chain from payload to command execution
- Correlated system activity using Splunk queries
- Demonstrated detection of post-exploitation behavior
  
---
## Screenshots

See /screenshots folder for:
- Nmap scan
- Payload execution
- Reverse shell
- Splunk logs
- Detection queries

---

## Learning Outcome

- Gained hands-on experience in attack simulation and detection
- Learned to analyze endpoint telemetry using Sysmon and Splunk
- Understood process-based detection techniques
- Developed basic detection engineering skills

---

  ## Future Improvements

- Implement real-time alerts in Splunk
- Create dashboards for SOC monitoring
- Simulate brute-force RDP attacks
- Enhance detection rules for advanced threats
