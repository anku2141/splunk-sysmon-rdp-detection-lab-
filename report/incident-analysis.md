# Incident Analysis Report – RDP Exploitation & Malware Execution

## Executive Summary
A simulated cyber attack was conducted by exploiting an exposed RDP service on the target machine. The attacker delivered a malicious payload disguised as a legitimate file, which established a reverse shell connection.
The attack activity was successfully detected using Sysmon logs and analyzed in Splunk.

## Environment Details

- Attacker Machine: Kali Linux
- Target Machine: Windows
- Monitoring Tool: Splunk Enterprise
- Logging Tool: Sysmon
- Log Index: index=main


## Attack Timeline

### Reconnaissance
- Network scan performed using Nmap
- Identified open ports:
  - 3389 (RDP)
  - 80 (HTTP)
  - 5985 (WinRM)

### Initial Access
- RDP identified as potential entry point

### Payload Creation
- Reverse shell payload created using msfvenom
- Payload named: Resume.pdf.exe

### Payload Delivery
- Hosted payload using Python HTTP server
- Downloaded on target machine

### Execution
- User executed malicious payload
- Reverse shell established

### Post-Exploitation
- Commands executed:
  - net user
  - net localgroup
  - ipconfig
 


## Evidence and Log Analysis

### Sysmon Event Analysis
- Event ID 1 (Process Creation) used for tracking execution

### Key Observations
- Suspicious process detected: Resume.pdf.exe
- Process spawned cmd.exe
- Multiple command-line activities observed

### Process Chain
Resume.pdf.exe → cmd.exe → system commands

### Splunk Findings
- Logs successfully ingested into Splunk
- Queries used to track malicious behavior



## Detection Queries

index=main EventCode=1  
index=main "resume.pdf.exe"  
index=main process_name="cmd.exe"  
index=main CommandLine="*net user*"  
index=main DestinationPort=3389


## Detection Analysis

- Identified suspicious executable masquerading as document
- Detected abnormal parent-child process relationship
- Observed execution of administrative commands
- Correlated events to confirm malicious activity


## MITRE ATT&CK Mapping

- T1021.001 – Remote Desktop Protocol
- T1078 – Valid Accounts
- T1105 – Ingress Tool Transfer
- T1059.001 – Command and Scripting Interpreter
- T1204 – User Execution


## Impact Assessment

- Unauthorized remote access established
- System enumeration performed
- Potential for further exploitation and lateral movement


## Conclusion

The simulated attack demonstrated how an attacker can exploit exposed services and execute malicious payloads. Using Sysmon and Splunk, the attack was successfully detected through process monitoring and log analysis.
This highlights the importance of endpoint visibility and detection engineering in identifying threats.


## Recommendations

- Enable real-time alerting in Splunk
- Restrict RDP access using firewall rules
- Monitor process creation events continuously
- Implement endpoint protection mechanisms
