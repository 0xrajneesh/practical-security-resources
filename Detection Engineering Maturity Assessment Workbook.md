# Detection Engineering Maturity Assessment Workbook

**Version 1.0 – Enterprise Evaluation Edition**

---

## Purpose

This workbook helps evaluate the maturity of a SOC’s Detection Engineering capability across:

- Detection Lifecycle
- Governance
- Validation
- Automation
- Metrics
- Architecture Alignment
- Continuous Improvement

---

# How to Use This Workbook

For each question:

Score your organization:

| Score | Meaning |
| --- | --- |
| 0 | Not implemented |
| 1 | Ad-hoc / informal |
| 2 | Partially implemented |
| 3 | Standardized |
| 4 | Optimized |
| 5 | Fully automated & continuously improved |

Track your score per section and calculate total maturity.

---

# SECTION 1️— Detection Strategy & Governance

### 1.1 Detection Ownership

- Is there a defined Detection Engineering owner?
- Are roles clearly separated between monitoring and detection creation?

Score: ___ / 5

---

### 1.2 Detection Lifecycle

- Is there a documented lifecycle? (Design → Deploy → Tune → Retire)
- Are detections reviewed periodically?

Score: ___ / 5

---

### 1.3 Threat Modeling Integration

- Are detections aligned with threat modeling outputs?
- Is business risk mapped to detection priority?

Score: ___ / 5

---

### 1.4 Version Control

- Are detection rules stored in Git?
- Is there change tracking?
- Are pull requests used?

Score: ___ / 5

---

### Section 1 Total: ___ / 20

---

# SECTION 2️— MITRE & Coverage Alignment

### 2.1 ATT&CK Mapping

- Are detections mapped to MITRE ATT&CK techniques?
- Is mapping standardized?

Score: ___ / 5

---

### 2.2 Coverage Visibility

- Do you track technique-level coverage?
- Can you identify detection gaps?

Score: ___ / 5

---

### 2.3 Technique Criticality Ranking

- Are high-impact TTPs prioritized?
- Is coverage weighted by business risk?

Score: ___ / 5

---

### 2.4 Log Dependency Awareness

- Do you track which logs each detection depends on?
- Are detection blind spots identified?

Score: ___ / 5

---

### Section 2 Total: ___ / 20

---

# SECTION 3️— Detection Quality & Tuning

### 3.1 False Positive Management

- Is FP rate measured?
- Is tuning documented?

Score: ___ / 5

---

### 3.2 Alert Actionability

- Does each alert have investigation guidance?
- Are runbooks attached?

Score: ___ / 5

---

### 3.3 Alert Quality Score

- Is alert precision measured?
- Is analyst feedback loop captured?

Score: ___ / 5

---

### 3.4 Performance Impact

- Are heavy queries optimized?
- Is SIEM performance monitored?

Score: ___ / 5

---

### Section 3 Total: ___ / 20

---

# SECTION 4️— Validation & Testing

### 4.1 Detection Testing

- Are detections tested against attack simulation?
- Is Atomic Red Team or similar used?

Score: ___ / 5

---

### 4.2 Drift Detection

- Are detections retested after log source changes?
- Is regression testing automated?

Score: ___ / 5

---

### 4.3 Detection Success Rate

- Do you measure detection reliability?
- Is testing documented?

Score: ___ / 5

---

### 4.4 Continuous Validation

- Is there scheduled validation cadence?
- Are failures tracked?

Score: ___ / 5

---

### Section 4 Total: ___ / 20

---

# SECTION 5️ — Automation & Engineering Practices

### 5.1 Detection-as-Code

- Are detections YAML-based (Sigma or similar)?
- Are pipelines automated?

Score: ___ / 5

---

### 5.2 CI/CD Integration

- Is there automated validation before deployment?
- Are rules tested in staging?

Score: ___ / 5

---

### 5.3 Deployment Governance

- Is change approval structured?
- Is rollback possible?

Score: ___ / 5

---

### 5.4 Documentation & Metadata

- Do rules include tags, references, owner, severity?
- Is documentation standardized?

Score: ___ / 5

---

### Section 5 Total: ___ / 20

---

# 📊 Final Maturity Scoring

Total Score: ___ / 100
