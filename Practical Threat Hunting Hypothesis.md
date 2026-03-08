# Threat Hunting Hypotheses

**Document Title:** Threat Hunting Hypotheses

**Version:** 1.1

**Author:** HAX Security Research Team

**Last Updated:** Dec  2025

**Classification:** Internal Security Operations Documentation

---

# Introduction

Threat hunting is a proactive security practice that focuses on identifying malicious or suspicious activity that may evade traditional detection mechanisms. While automated detection systems such as SIEM alerts, IDS signatures, and EDR rules provide important visibility, sophisticated adversaries often operate using techniques that bypass these controls.

This document outlines a set of **practical threat hunting hypotheses** designed to guide analysts in investigating potential attacker behaviors across enterprise environments. Each hypothesis is aligned with relevant **MITRE ATT&CK techniques**, enabling structured and intelligence-driven investigations.

These hypotheses serve as a foundation for conducting proactive threat hunts and improving overall detection capabilities within the Security Operations Center (SOC).

---

# Objective

The objective of this document is to provide a structured set of **testable threat hunting hypotheses** that enable security analysts to proactively search for indicators of compromise within organizational telemetry.

Specifically, this document aims to:

- Identify potential attacker techniques that may bypass existing detections.
- Guide SOC analysts in conducting **hypothesis-driven threat hunts**.
- Improve visibility across endpoint, network, identity, and cloud environments.
- Support the development of **new detection rules and investigation playbooks**.
- Align threat hunting activities with **MITRE ATT&CK techniques**.

---

# Scope

This document applies to threat hunting activities conducted within the organization's Security Operations Center and covers telemetry collected from the following environments:

### Endpoint Systems

- Windows endpoints and servers
- Linux systems

### Network Infrastructure

- Network traffic logs
- DNS activity
- Firewall logs

### Identity and Authentication Systems

- Active Directory authentication logs
- Privileged account activity

### Cloud Platforms

- Cloud API logs
- Identity and access management events

The hypotheses defined in this document are intended to support **proactive investigation of suspicious behavior across these environments using available telemetry and monitoring tools**.

---

# Hypothesis 1 — Suspicious PowerShell Download Activity

### MITRE ATT&CK Technique

**T1059.001 — Command and Scripting Interpreter: PowerShell**

### Hypothesis

An attacker may use **PowerShell to download and execute malicious payloads from external infrastructure**.

### Data Sources

| Source | Log |
| --- | --- |
| Windows Endpoint | Sysmon Event ID 1 |
| Windows Endpoint | PowerShell Script Block Logging |

### Investigation Procedure

1. Identify PowerShell processes executing commands containing HTTP or HTTPS URLs.
2. Review command-line arguments for encoded or obfuscated commands.
3. Investigate parent processes responsible for launching PowerShell.
4. Correlate activity with outbound network connections.

### Indicators

- Encoded PowerShell commands
- External IP connections
- PowerShell launched by Office applications

---

# Hypothesis 2 — Credential Dumping via LSASS Access

### MITRE ATT&CK Technique

**T1003.001 — OS Credential Dumping: LSASS Memory**

### Hypothesis

An attacker may attempt to extract credentials from **LSASS process memory** to obtain administrative privileges.

### Data Sources

| Source | Log |
| --- | --- |
| Sysmon | Event ID 10 |
| Windows Security Logs | Process Access |

### Investigation Procedure

1. Identify processes accessing `lsass.exe`.
2. Investigate unsigned or uncommon binaries performing memory reads.
3. Correlate with process creation events.

### Indicators

- Tools like `mimikatz`
- Suspicious memory access
- Non-system process interacting with LSASS

---

# Hypothesis 3 — Malicious Scheduled Task Creation

### MITRE ATT&CK Technique

**T1053.005 — Scheduled Task**

### Hypothesis

An attacker may establish persistence by creating **scheduled tasks that execute malicious commands**.

### Data Sources

| Source | Log |
| --- | --- |
| Windows Logs | Event ID 4698 |
| Sysmon | Process Creation |

### Investigation Procedure

1. Identify newly created scheduled tasks.
2. Review the command executed by the task.
3. Investigate task creator account.

### Indicators

- Tasks executing from temp directories
- Unknown binaries
- Tasks created by compromised accounts

---

# Hypothesis 4 — Suspicious Administrative Logins

### MITRE ATT&CK Technique

**T1078 — Valid Accounts**

### Hypothesis

An attacker may use **compromised administrative credentials** to access systems.

### Data Sources

| Source | Log |
| --- | --- |
| Windows Security Logs | Event ID 4624 |
| Active Directory Logs | Authentication Events |

### Investigation Procedure

1. Identify logins outside normal working hours.
2. Review login source IP addresses.
3. Detect new geographic locations.

---

# Hypothesis 5 — Command and Control over Web Protocols

### MITRE ATT&CK Technique

**T1071.001 — Application Layer Protocol: Web Protocols**

### Hypothesis

A compromised system may communicate with a **command and control server using HTTP or HTTPS**.

### Data Sources

| Source | Log |
| --- | --- |
| Zeek | HTTP Logs |
| Firewall | Network Logs |

### Investigation Procedure

1. Identify periodic outbound HTTP requests.
2. Investigate domains with low reputation.
3. Analyze unusual user agents.

---

# Hypothesis 6 — DNS Tunneling for Data Exfiltration

### MITRE ATT&CK Technique

**T1071.004 — Application Layer Protocol: DNS**

### Hypothesis

An attacker may use **DNS tunneling to exfiltrate data** from the internal network.

### Data Sources

| Source | Log |
| --- | --- |
| DNS Logs | Query Logs |
| Zeek | DNS Logs |

### Investigation Procedure

1. Identify domains with unusually long subdomains.
2. Detect high frequency DNS queries.
3. Investigate suspicious entropy patterns.

---

# Hypothesis 7 — Office Document Spawning Processes

### MITRE ATT&CK Technique

**T1204 — User Execution**

### Hypothesis

A malicious document may execute **child processes to download malware**.

### Investigation Procedure

1. Identify Office processes spawning PowerShell or CMD.
2. Review command-line parameters.
3. Investigate downloaded files.

---

# Hypothesis 8 — Suspicious Service Creation

### MITRE ATT&CK Technique

**T1543.003 — Windows Service**

### Hypothesis

An attacker may install **malicious services to maintain persistence**.

### Data Sources

Windows Event Logs — Event ID 7045

### Investigation

1. Identify newly created services.
2. Investigate service executable path.
3. Validate service legitimacy.

---

# Hypothesis 9 — Persistence via Registry Run Keys

### MITRE ATT&CK Technique

**T1547.001 — Registry Run Keys**

### Hypothesis

An attacker may establish persistence through **registry autorun entries**.

### Investigation

1. Identify new registry run key entries.
2. Review associated executable paths.
3. Investigate unknown binaries.

---

# Hypothesis 10 — Lateral Movement via SMB

### MITRE ATT&CK Technique

**T1021.002 — SMB/Windows Admin Shares**

### Hypothesis

An attacker may move laterally across the network using **SMB administrative shares**.

### Investigation

1. Identify SMB connections between endpoints.
2. Detect file transfers to admin shares.
3. Investigate credential usage.

---

# Hypothesis 11 — Kerberoasting Activity

### MITRE ATT&CK Technique

**T1558.003 — Kerberoasting**

### Hypothesis

An attacker may request Kerberos service tickets to perform **offline password cracking**.

### Data Sources

Windows Event Logs — Event ID 4769

### Investigation

1. Identify large numbers of service ticket requests.
2. Detect unusual accounts requesting multiple SPNs.

---

# Hypothesis 12 — Suspicious Use of Living-off-the-Land Binaries

### MITRE ATT&CK Technique

**T1218 — Signed Binary Proxy Execution**

### Hypothesis

Attackers may abuse legitimate Windows utilities such as:

- `rundll32`
- `mshta`
- `certutil`

to execute malicious commands.

# Hypothesis 13 — Remote Command Execution via WMI

### MITRE ATT&CK Technique

**T1047 — Windows Management Instrumentation**

### Hypothesis

An attacker may execute **remote commands on other systems using WMI** to perform lateral movement.

### Data Sources

Windows Event Logs — Event ID 4688

Sysmon — Event ID 1

### Investigation

1. Identify execution of `wmic.exe` commands.
2. Detect remote process execution targeting other hosts.

---

# Hypothesis 14 — Lateral Movement via Remote Desktop Protocol

### MITRE ATT&CK Technique

**T1021.001 — Remote Desktop Protocol**

### Hypothesis

An attacker may use **RDP sessions to move laterally across systems using compromised credentials**.

### Data Sources

Windows Security Logs — Event ID 4624 (Logon Type 10)

### Investigation

1. Identify RDP logins from unusual systems.
2. Detect administrative accounts logging into multiple hosts.

---

# Hypothesis 15 — Suspicious Archive Creation Before Data Exfiltration

### MITRE ATT&CK Technique

**T1560 — Archive Collected Data**

### Hypothesis

An attacker may compress files using archive utilities before **exfiltrating sensitive data**.

### Data Sources

Sysmon — Event ID 1

### Investigation

1. Identify execution of tools such as `7z`, `rar`, or `zip`.
2. Detect archive files created in temporary directories.

---

# Hypothesis 16 — Internal Network Port Scanning

### MITRE ATT&CK Technique

**T1046 — Network Service Discovery**

### Hypothesis

An attacker may perform **internal port scanning to identify accessible services** within the network.

### Data Sources

Zeek — Connection Logs

Firewall Logs

### Investigation

1. Identify hosts connecting to multiple ports across many systems.
2. Detect rapid connection attempts from a single host.

---

# Hypothesis 17 — Unauthorized Account Creation

### MITRE ATT&CK Technique

**T1136 — Create Account**

### Hypothesis

An attacker may create **new user accounts to maintain persistence within the environment**.

### Data Sources

Windows Security Logs — Event ID 4720

### Investigation

1. Identify newly created accounts.
2. Investigate accounts created outside normal administrative processes.

---

# Hypothesis 18 — Suspicious SSH Access

### MITRE ATT&CK Technique

**T1021.004 — SSH**

### Hypothesis

An attacker may gain access to Linux systems using **unauthorized SSH sessions**.

### Data Sources

Linux Authentication Logs — `/var/log/auth.log`

### Investigation

1. Identify SSH logins from unfamiliar IP addresses.
2. Detect repeated authentication attempts.

---

# Hypothesis 19 — Process Injection Activity

### MITRE ATT&CK Technique

**T1055 — Process Injection**

### Hypothesis

An attacker may inject malicious code into **legitimate processes to evade detection**.

### Data Sources

Sysmon — Event ID 8

### Investigation

1. Identify processes performing injection activity.
2. Investigate unusual parent-child process relationships.

---

# Hypothesis 20 — Data Exfiltration Over Command and Control Channel

### MITRE ATT&CK Technique

**T1041 — Exfiltration Over C2 Channel**

### Hypothesis

A compromised host may **transfer sensitive data to attacker infrastructure through command-and-control communication channels**.

### Data Sources

Zeek — HTTP Logs

Firewall Logs

### Investigation

1. Identify large outbound data transfers.
2. Investigate connections to suspicious external domains.

---

# Hypothesis 21 — Lateral Movement Using PsExec

### MITRE ATT&CK Technique

**T1021.002 — SMB/Windows Admin Shares**

### Hypothesis

An attacker may use **PsExec to execute commands on remote systems for lateral movement**.

### Data Sources

Windows Security Logs — Event ID 4688

### Investigation

1. Identify execution of `psexec.exe`.
2. Detect remote service creation across systems.

---

# Hypothesis 22 — Persistence via Startup Folder

### MITRE ATT&CK Technique

**T1547.001 — Boot or Logon Autostart Execution**

### Hypothesis

An attacker may place malicious executables in **startup folders to ensure execution during system login**.

### Data Sources

File Monitoring Logs

### Investigation

1. Identify files placed in startup directories.
2. Investigate unknown executables configured to run on login.

---

# Hypothesis 23 — Suspicious Browser Credential Access

### MITRE ATT&CK Technique

**T1555 — Credentials from Password Stores**

### Hypothesis

An attacker may attempt to **extract credentials stored in web browsers**.

### Data Sources

Endpoint Logs

File Access Logs

### Investigation

1. Detect processes accessing browser credential databases.
2. Investigate suspicious file reads from browser profile directories.

---

# Hypothesis 24 — Excessive Failed Login Attempts

### MITRE ATT&CK Technique

**T1110 — Brute Force**

### Hypothesis

An attacker may attempt to **guess passwords through repeated login attempts**.

### Data Sources

Windows Security Logs — Event ID 4625

### Investigation

1. Identify multiple failed login attempts for the same account.
2. Detect authentication attempts from a single source IP.

---

# Hypothesis 25 — Suspicious Use of Encoded Commands

### MITRE ATT&CK Technique

**T1027 — Obfuscated Files or Information**

### Hypothesis

An attacker may use **encoded commands to evade detection and hide malicious activity**.

### Data Sources

Sysmon — Event ID 1

### Investigation

1. Identify command lines containing Base64 encoded strings.
2. Detect use of encoding flags in scripting environments.

---

# Hypothesis 26 — Suspicious Network Beaconing

### MITRE ATT&CK Technique

**T1071 — Application Layer Protocol**

### Hypothesis

A compromised host may communicate with attacker infrastructure through **periodic beaconing traffic**.

### Data Sources

Zeek — Connection Logs

### Investigation

1. Identify regular network connections at fixed intervals.
2. Investigate low-volume recurring traffic to external domains.

---

# Hypothesis 27 — Enumeration of Domain Accounts

### MITRE ATT&CK Technique

**T1087 — Account Discovery**

### Hypothesis

An attacker may enumerate **domain accounts to identify privileged users**.

### Data Sources

Sysmon — Event ID 1

### Investigation

1. Identify commands such as `net user` or `net group`.
2. Detect enumeration executed from unusual hosts.

---

# Hypothesis 28 — System Information Discovery

### MITRE ATT&CK Technique

**T1082 — System Information Discovery**

### Hypothesis

An attacker may collect **system configuration information** from compromised hosts.

### Data Sources

Sysmon — Event ID 1

### Investigation

1. Identify commands like `systeminfo`, `whoami`, or `hostname`.
2. Detect reconnaissance commands executed sequentially.

---

# Hypothesis 29 — Suspicious Use of Credential Dumping Tools

### MITRE ATT&CK Technique

**T1003 — OS Credential Dumping**

### Hypothesis

An attacker may use tools designed to **extract credentials from system memory or credential stores**.

### Data Sources

Sysmon — Event ID 1

Sysmon — Event ID 10

### Investigation

1. Identify execution of known credential dumping utilities.
2. Investigate processes interacting with credential storage mechanisms.

---

# Hypothesis 30 — Suspicious Cloud Privilege Escalation

### MITRE ATT&CK Technique

**T1098 — Account Manipulation**

### Hypothesis

An attacker may attempt to escalate privileges by **modifying cloud IAM roles or permissions**.

### Data Sources

Cloud API Logs — IAM Activity

### Investigation

1. Identify changes to IAM policies or roles.
2. Detect privilege assignments to previously low-privileged accounts.

## Conclusion

Threat hunting enables SOC teams to move from **reactive alert monitoring to proactive threat detection**. By using a **hypothesis-driven approach aligned with MITRE ATT&CK**, analysts can systematically investigate attacker behaviors across endpoint, network, identity, and cloud telemetry.

The hypotheses outlined in this document provide a **practical foundation for identifying suspicious activity that may bypass automated detections**. Over time, findings from threat hunts should be operationalized into **detection rules, investigation playbooks, and improved visibility**, strengthening the overall security posture of the organization.
