## AWS Cloud Incident Investigation
### Overview

Investigated a simulated enterprise cloud security incident involving compromised AWS IAM credentials, unauthorized S3 bucket exposure, cryptomining activity, endpoint compromise, and privilege escalation. The investigation combined SIEM analysis, AWS CloudTrail logs, endpoint telemetry, DNS activity, and MITRE ATT&CK mapping to reconstruct the complete attack lifecycle.

### Objectives

- Validate SIEM alerts
- Identify attacker activity
- Reconstruct attack timeline
- Determine incident scope
- Classify incident severity
- Recommend remediation actions

### Environment

- Splunk Enterprise
- AWS CloudTrail
- Windows Event Logs
- DNS Logs
- Endpoint Telemetry
- Linux Web Server Logs

### Tools Used

- Splunk SPL
- AWS CloudTrail
- MITRE ATT&CK
- AWS IAM
- DNS Analysis
- Endpoint Telemetry
- Threat Hunting
- Log Correlation

### Skills Demonstrated

- Incident Response
- Threat Hunting
- Log Analysis
- Cloud Security
- AWS Investigation
- IOC Identification
- Timeline Reconstruction
- Root Cause Analysis
- MITRE ATT&CK Mapping
- Incident Documentation

### Deliverables

- Incident Triage Report
- Investigation Workbook
- SPL Queries
- Timeline Reconstruction

### Key Findings

- Confirmed True Positive SIEM alert
- Compromised AWS IAM credentials
- Unauthorized S3 bucket exposure
- Public data staging
- Cryptomining activity
- Endpoint compromise
- Root privilege escalation
- Command-and-control communications
- MITRE ATT&CK technique mapping
- Executive remediation recommendations