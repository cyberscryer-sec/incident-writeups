# Case Study: Suspicious Encoded PowerShell Execution

## Summary
An alert was generated for encoded PowerShell execution on a Windows endpoint. Analysis focused on determining whether activity was administrative automation or potential malicious post-exploitation behavior.

## Alert Context
Detection: Encoded PowerShell Command Execution  
Source: Endpoint telemetry (EDR)  
Technique: MITRE ATT&CK T1059.001 – PowerShell  

## Timeline
- 09:14 – Encoded PowerShell command executed by user workstation
- 09:15 – Outbound network connection to external IP observed
- 09:17 – Additional child process spawned (cmd.exe)

## Evidence & Analysis
- Decoded Base64 PowerShell payload
- Identified obfuscated download cradle pattern
- Verified external IP reputation
- Reviewed user login context and recent activity
- Checked Defender/CrowdStrike process tree relationships

## Assessment
Behavior consistent with suspicious post-exploitation activity. Obfuscation and external connectivity patterns increased likelihood of malicious intent.

## Containment & Remediation
- Isolated endpoint
- Reset user credentials
- Blocked external IP at firewall
- Initiated deeper host forensic review

## Detection Improvement
Recommend tuning detection to:
- Flag encoded commands with outbound network connections
- Elevate severity when child process spawns cmd.exe
