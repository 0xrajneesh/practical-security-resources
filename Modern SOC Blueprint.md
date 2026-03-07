# Modern SOC Blueprint

**Document Type:** Method of Procedure (MOP)

**Prepared By:** Rajneesh Gupta

**Organization:** HaxSecurity.com

**Version:** 1.1

---

## Introduction

This document outlines a practical blueprint for designing and implementing a **modern Security Operations Center (SOC)**. The goal is to provide a structured approach to building SOC capabilities that focus on **visibility, detection engineering, automation, and effective incident response**.

Many organizations deploy multiple security tools but lack a clear operational framework to integrate them effectively. This blueprint addresses that challenge by defining a **practical SOC architecture, operational workflows, and detection strategies** aligned with modern threat landscapes.

---

## Objective

The objective of this document is to provide a **clear and actionable framework** for building or improving a Security Operations Center. The focus is on enabling security teams to:

- Establish centralized security monitoring
- Implement detection capabilities mapped to adversary techniques
- Integrate threat intelligence and automation
- Improve incident detection and response efficiency

This blueprint is intended to support organizations in developing a **detection-driven SOC capable of monitoring modern enterprise environments including endpoints, networks, cloud infrastructure, and identity systems.**

---

## Scope

This document covers key components required for a modern SOC, including:

- SOC architecture design
- Log and telemetry collection
- SIEM implementation
- Detection engineering practices
- Automation and response workflows

The guidance provided is **vendor-neutral** and can be adapted using either open-source or enterprise security technologies.

---

## Author Note

This blueprint reflects operational practices and architectural approaches used in **real-world SOC environments and blue team training programs developed at HaxSecurity**. The intent is to provide a practical reference that security teams can use when designing or maturing their SOC capabilities.

# Modern SOC Architecture (Practical Blueprint)

## Core Operational Flow

```
Endpoints / Network / Cloud / Identity
              ↓
         Log Collectors
   (Agents / Syslog / APIs)
              ↓
        Log Pipeline Layer
  (Parsing, Normalization, Enrichment)
              ↓
              SIEM
   (Correlation, Dashboards, Alerts)
              ↓
       Detection Engineering
    (ATT&CK mapped detections)
              ↓
       SOAR / Automation
   (Response playbooks & actions)
              ↓
        Incident Response
   (Investigation & containment)
```

Each layer has **clear operational responsibilities**.

---

# 1. Visibility Layer (Telemetry Collection)

The SOC starts with **collecting security telemetry from critical infrastructure**.

## Endpoint Telemetry

Recommended tools

- Wazuh

[https://wazuh.com](https://wazuh.com/)

- Microsoft Defender for Endpoint

https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/

- Sysmon

https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon

### Data collected

- process creation
- command execution
- registry changes
- file creation
- network connections

### Practical Use Case

Detect **malicious PowerShell execution**

```
EventID 1 (Process Creation)
process = powershell.exe
command_line contains downloadstring
```

Mapped to:

MITRE ATT&CK

T1059 — Command and Scripting Interpreter

---

## Network Telemetry

Tools

- Zeek

[https://zeek.org](https://zeek.org/)

- Suricata

[https://suricata.io](https://suricata.io/)

- NetFlow / Firewall logs

### Data collected

- DNS queries
- HTTP requests
- TLS fingerprints
- network connections

### Practical Use Case

Detect **command and control communication**

Example detection:

```
DNS query to known malicious domain
OR
Beaconing traffic pattern
```

MITRE ATT&CK

T1071 — Application Layer Protocol

---

## Cloud Telemetry

For cloud infrastructure, API logs are critical.

### AWS Logs

- AWS CloudTrail

• VPC Flow Logs

• GuardDuty findings

Docs

https://docs.aws.amazon.com/awscloudtrail

### Example Use Case

Detect **privilege escalation**

```
eventName = AttachUserPolicy
policy = AdministratorAccess
```

MITRE ATT&CK

T1078 — Valid Accounts

---

## Identity Telemetry

Identity is often the **primary attack vector**.

Sources

- Active Directory
- Okta
- Azure AD
- VPN authentication logs

Example detection

```
Multiple failed logins
followed by successful login
from new country
```

MITRE ATT&CK

T1110 — Brute Force

---

# 2. Log Pipeline (Data Engineering Layer)

Logs must be **processed before reaching SIEM**.

Typical pipeline architecture

```
Log Sources
   ↓
Log Forwarder
   ↓
Log Parser
   ↓
Normalization
   ↓
Enrichment
   ↓
SIEM
```

Recommended tools

Vector

[https://vector.dev](https://vector.dev/)

Fluentd

[https://www.fluentd.org](https://www.fluentd.org/)

Logstash

https://www.elastic.co/logstash

Kafka (for high scale)

---

## Pipeline Enrichment Example

Add threat intel context.

```
Incoming Log
IP = 185.220.101.10

Enrichment
GeoIP → Germany
Reputation → Tor Exit Node
ThreatIntel → Known malicious
```

Result

SOC analysts see **risk context immediately**.

---

# 3. SIEM Layer (Central Analysis Engine)

SIEM responsibilities

- log storage
- correlation rules
- alert generation
- dashboards
- investigations

Popular SIEM platforms

| SIEM | Link |
| --- | --- |
| Splunk | [https://www.splunk.com](https://www.splunk.com/) |
| Elastic Security | https://www.elastic.co/security |
| Microsoft Sentinel | https://learn.microsoft.com/en-us/azure/sentinel |

---

## Example Detection Rule

Detect suspicious lateral movement.

```
source host A
connects to
multiple hosts via SMB

within 1 minute
```

Mapped to

MITRE ATT&CK

T1021 — Lateral Movement

---

# 4. Detection Engineering Layer

Modern SOCs treat detections as **engineering problems**.

Framework used

MITRE ATT&CK

[https://attack.mitre.org](https://attack.mitre.org/)

Detection format

Sigma rules

[https://sigmahq.io](https://sigmahq.io/)

Example Sigma rule

```
title: Suspicious PowerShell Download

logsource:
  product: windows

detection:
  selection:
    Image: powershell.exe
    CommandLine: "*downloadstring*"

condition: selection
```

Detection coverage should map to **ATT&CK techniques**.

Example coverage table

| Technique | Detection |
| --- | --- |
| Credential Dumping | LSASS access |
| Lateral Movement | SMB admin share |
| Persistence | scheduled tasks |

---

# 5. Threat Intelligence Integration

Threat intelligence improves **alert context and prioritization**.

Platforms

MISP

[https://www.misp-project.org](https://www.misp-project.org/)

OpenCTI

[https://www.opencti.io](https://www.opencti.io/)

VirusTotal

[https://www.virustotal.com](https://www.virustotal.com/)

---

## Threat Intel Workflow

```
Alert triggered
      ↓
Extract IOC
(IP / Domain / Hash)
      ↓
Query Threat Intel
      ↓
Add context
      ↓
Prioritize incident
```

Example enrichment

```
IP Address: 103.45.67.12
Campaign: FIN7
Malware: TrickBot
Confidence: High
```

---

# 6. SOAR and Automation

Automation eliminates repetitive analyst tasks.

Tools

Shuffle

[https://shuffler.io](https://shuffler.io/)

n8n

[https://n8n.io](https://n8n.io/)

Cortex XSOAR

https://www.paloaltonetworks.com/cortex/cortex-xsoar

---

## Automated Investigation Workflow

Example playbook

```
Alert: Suspicious IP detected
        ↓
Extract IP
        ↓
Check AbuseIPDB
        ↓
Check VirusTotal
        ↓
Check internal logs
        ↓
Create investigation ticket
```

Automation reduces response time dramatically.

---

# 7. Incident Response Operations

SOC must follow **structured response processes**.

Framework

NIST Incident Response

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Response lifecycle

```
Preparation
      ↓
Detection
      ↓
Analysis
      ↓
Containment
      ↓
Eradication
      ↓
Recovery
```

Example containment actions

- disable compromised account
- isolate infected endpoint
- block malicious domain

---

# 8. Threat Hunting Layer

Threat hunting is proactive security.

Framework

PEAK Threat Hunting Framework

Stages

```
Prepare
Execute
Analyze
Knowledge
```

Example hunt

Hypothesis

```
Attackers may abuse PowerShell
for lateral movement
```

Hunting query

```
process = powershell.exe
network connections > 10
within 60 seconds
```

---

# 9. SOC Operational Metrics

Metrics measure SOC performance.

Key SOC KPIs

| Metric | Purpose |
| --- | --- |
| MTTD | detection speed |
| MTTR | response speed |
| Alert fidelity | alert quality |
| ATT&CK coverage | detection maturity |

Example dashboard

```
MTTD: 12 minutes
MTTR: 45 minutes
False positives: 18%
```

---

# 10. Practical Open-Source SOC Stack

Example **realistic open-source SOC**.

```
Endpoint Security
    Wazuh

Network Monitoring
    Suricata + Zeek

Log Pipeline
    Vector

SIEM
    Elastic Security

Threat Intelligence
    MISP

Automation
    n8n
```

Deployment architecture

```
Endpoints → Wazuh Agents
Network → Suricata Sensors
Cloud → CloudTrail

          ↓

Vector Pipeline

          ↓

Elastic SIEM

          ↓

n8n Automation

          ↓

SOC Analysts
```

This stack can run on **3–4 servers**.

---

# 11. Example End-to-End SOC Detection Flow

Real attack example:

### Step 1 — Attacker initial access

Phishing email delivered.

```
Email gateway logs event
```

---

### Step 2 — Malware execution

Endpoint logs

```
powershell.exe downloadstring
```

---

### Step 3 — SIEM detection

Detection rule triggers.

```
Alert created
```

---

### Step 4 — SOAR automation

```
IP reputation lookup
hash lookup
threat intel enrichment
```

---

### Step 5 — Incident response

SOC analyst

```
isolate host
disable account
start investigation
```

---

# Final Principle

A practical SOC must focus on **four pillars**

```
Visibility
Detection Engineering
Automation
Incident Response
```

Organizations that invest in these capabilities build **resilient, modern SOCs** capable of detecting advanced threats.
