# Case Study: Suspicious Lateral Movement in Windows Domain Environment

## Incident Type
Suspected Lateral Movement via SMB + Remote Service Creation

## Severity
High

## Date/Time Window
Jan 14, 2026 | 02:12–03:04 UTC

## Environment
Windows Domain (Active Directory)  
Endpoint EDR (Microsoft Defender for Endpoint)  
SIEM (Splunk / Microsoft Sentinel)

## Responder
Julia Tam — SOC Analyst

---

# 1) Executive Summary

- A non-administrative domain user account authenticated to multiple internal servers within a short time window.
- Shortly after authentication, remote service creation events and SMB administrative share access were observed.
- Activity originated from a user workstation not typically used for server administration.
- No confirmed data exfiltration; containment actions were taken proactively.
- Behavior consistent with early-stage lateral movement following credential compromise.

Current Status: Contained. Monitoring ongoing.

---

# 2) Customer Impact

**Business Impact:**  
Potential unauthorized administrative access to internal servers.

**Scope:**  
- 1 user account involved  
- 3 internal servers accessed  
- No additional user accounts observed performing similar behavior  

**Risk Statement:**  
If not contained, attacker could escalate privileges, deploy ransomware, or move toward domain controller compromise.

---

# 3) Key Findings

## Indicators Observed

- Multiple successful Kerberos authentication events (Event ID 4624, Logon Type 3)
- Network share access to `\\SERVER01\ADMIN$`
- Service creation event (Event ID 7045)
- Suspicious process tree:
  - `cmd.exe`
  - `sc.exe create`
  - Remote service execution

## Evidence Sources

- Windows Security Event Logs
- Microsoft Defender for Endpoint process telemetry
- Splunk authentication dashboards
- Sysmon Event ID 3 (Network connections)

## Confirmed

- Lateral authentication from workstation `WS-2241`
- Remote service created on `SERVER02`
- Administrative share access via SMB

## Unconfirmed / Needs Validation

- Initial credential access vector (phishing vs credential reuse)
- Whether additional hosts were accessed outside log retention window

---

# 4) Timeline of Events (UTC)

| Time  | Event | Source | Notes |
|-------|-------|--------|-------|
| 02:12 | Successful logon (Type 3) to SERVER01 | Windows Security Log | Origin: WS-2241 |
| 02:15 | SMB connection to ADMIN$ share | Sysmon | Administrative share access |
| 02:18 | Service creation on SERVER02 | Event ID 7045 | Service name: `UpdateCheck` |
| 02:21 | Process execution via remote service | Defender EDR | `cmd.exe /c powershell -enc ...` |
| 02:27 | Additional logon to SERVER03 | Windows Security Log | Same user account |
| 02:42 | SOC alert triggered (lateral movement correlation) | SIEM | Correlated multi-host auth |
| 02:55 | Endpoint isolation initiated | Defender | WS-2241 isolated |
| 03:04 | Account disabled pending investigation | AD Admin | Containment completed |

---

# 5) Analysis

### Why This Was Suspicious

- User does not belong to domain admin or server admin groups.
- Workstation is not part of IT administrative subnet.
- Authentication burst across multiple servers within 15 minutes.
- Remote service creation + encoded PowerShell execution strongly associated with lateral movement techniques (MITRE ATT&CK T1021, T1543).

### Behavioral Pattern Observed

1. Credential usage on internal server
2. Administrative share access
3. Service creation for remote execution
4. Additional lateral authentication attempts

This sequence is consistent with attacker playbooks post-credential compromise.

---

# 6) Actions Taken

- Isolated originating workstation (`WS-2241`)
- Disabled impacted user account
- Reset credentials + enforced MFA re-registration
- Initiated EDR scan on accessed servers
- Reviewed domain controller logs for additional suspicious authentications
- Blocked suspicious service artifact

---

# 7) Recommended Next Steps

## Immediate (0–2 hours)
- Review LSASS access telemetry for credential dumping indicators
- Audit domain admin group membership changes
- Confirm no persistence mechanisms remain on accessed servers

## Short Term (24–72 hours)
- Review Kerberos TGT/TGS request patterns for anomalous service ticket requests
- Conduct targeted threat hunt for remote service creation events (Event ID 7045)
- Validate Conditional Access and privileged access workflows

## Preventive (1–2 weeks)
- Implement alerting for:
  - Non-admin accounts accessing ADMIN$ shares
  - Remote service creation from workstations
  - Authentication bursts across multiple servers
- Consider privileged access workstation (PAW) model enforcement
- Conduct password hygiene review for impacted user group

---

# 8) Detection Improvement Opportunities

- Correlate:
  - Event ID 4624 (Logon Type 3) +
  - Event ID 7045 (Service Creation) +
  - SMB ADMIN$ access
- Elevate severity when:
  - Non-admin account authenticates to >2 servers within 20 minutes
- Add detection for encoded PowerShell execution triggered by remote services

---

# 9) Handoff Notes (Shift Transition)

Current State: Contained. No further lateral authentication observed post-isolation.

Monitor:
- New remote service creation events
- Repeat authentication from disabled account
- Any additional authentication bursts involving peer accounts

Escalate If:
- Domain controller authentication observed
- Additional accounts exhibit similar behavior
- Privileged group membership changes detected
