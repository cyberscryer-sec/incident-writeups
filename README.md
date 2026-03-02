# Incident Investigation Write-Ups

This repository contains sanitized example incident investigations demonstrating alert triage, analysis methodology, documentation clarity, and remediation guidance.

Each write-up follows a consistent SOC-ready structure:

- Summary
- Alert Context
- Timeline
- Evidence & Analysis
- Assessment
- Containment & Remediation Recommendations
- Follow-ups / Detection Improvements

---

## Case Studies

### 1) [Suspicious Encoded Powershell Execution](./powershell-encoded-command-investigation.md)
Investigated an alert for Base64-encoded PowerShell execution, decoded and analyzed the payload and process chain to assess malicious intent, and documented containment actions plus detection-tuning recommendations.

### 2) [Suspicious Lateral Movement in Windows Domain](./lateral-mvmt-domain-investigation.md)
Investigated a burst of cross-server authentications from a non-admin workstation, confirmed SMB ADMIN$ access and remote service creation consistent with lateral movement, and documented containment steps plus detection tuning opportunities.

---

These write-ups demonstrate how I structure investigations, communicate findings, and provide actionable remediation guidance.

A reusable [incident summary template](./incident-summary-template.md) is included in this repository.
