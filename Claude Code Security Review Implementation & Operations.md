## Claude Code Security Review – Implementation & Operations

---

# 1. Objective

To integrate **automated AI-driven security review** into the pull request (PR) workflow in order to:

- Detect vulnerabilities in code changes before merge
- Enforce secure coding standards
- Reduce AppSec review effort
- Provide developers with actionable fixes

---

# 2. Scope

This procedure applies to:

- All GitHub repositories (application code)
- All pull requests targeting protected branches (main, release)
- All development teams
- CI/CD pipelines using GitHub Actions

---

# 3. Roles & Responsibilities

## 3.1 AppSec Team

- Define security policy (severity thresholds)
- Monitor findings and trends
- Tune implementation if needed
- Audit effectiveness

## 3.2 Development Team

- Fix vulnerabilities identified in PR
- Follow secure coding practices
- Do not bypass security checks

## 3.3 DevOps Team

- Implement GitHub Actions
- Maintain secrets and pipeline integrity
- Enforce branch protection rules

---

# 4. Prerequisites

## 4.1 Access Requirements

- GitHub repository admin access
- Anthropic API key

---

## 4.2 Store API Key

Navigate to:

```
Repository → Settings → Secrets → Actions → New Repository Secret
```

Add:

```
Name: ANTHROPIC_API_KEY
Value: <your_api_key>
```

---

## 4.3 Branch Protection Setup

Enable for `main` branch:

- Require pull request before merge
- Require status checks:
    - `Claude Security Review`
- Restrict direct pushes

---

# 5. Implementation Procedure

---

## Step 1: Create Workflow File

Create:

```
.github/workflows/claude-security-review.yml
```

---

## Step 2: Add Base Configuration

```
name: Claude Security Review

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  security-review:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Run Claude Security Review
        uses: anthropics/claude-code-security-review@main
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

---

## Step 3: Optional – Restrict to Specific Paths

To scan only critical code (e.g., backend):

```
on:
  pull_request:
    paths:
      -"backend/**"
      -"api/**"
```

---

## Step 4: Optional – Ignore Non-Sensitive Files

```
on:
  pull_request:
    paths-ignore:
      -"*.md"
      -"docs/**"
      -"README.md"
```

---

## Step 5: Enforce Policy in Workflow

Example: Fail build if high severity issues detected

```
env:
  FAIL_ON_SEVERITY:"high"
```

(Depending on customization support in your wrapper implementation)

---

# 6. Execution Flow (Runtime)

---

## 6.1 Developer Action

Developer submits PR:

```
# vulnerable code example
query="SELECT * FROM users WHERE username = '"+username+"'"
```

---

## 6.2 Pipeline Execution

- GitHub Action triggers
- Diff is extracted
- Sent to Claude for analysis

---

## 6.3 Output in PR

Claude posts inline comment:

```
SQL Injection Vulnerability Detected

Description:
User-controlled input is concatenated into SQL query.

Severity:
High

Recommended Fix:
Use parameterized queries.
```

---

## 6.4 Developer Remediation

Fix applied:

```
cursor.execute(
"SELECT * FROM users WHERE username = %s",
    (username,)
)
```

---

## 6.5 Re-run Pipeline

- Action re-triggers
- If no high issues → PR can proceed

---

# 7. Practical Use Cases with Code

---

# 7.1 SQL Injection Detection

## Vulnerable Code

```
defget_user(username):
query="SELECT * FROM users WHERE username = '"+username+"'"
returndb.execute(query)
```

---

## Secure Version

```
defget_user(username):
query="SELECT * FROM users WHERE username = %s"
returndb.execute(query, (username,))
```

---

# 7.2 Command Injection

## Vulnerable Code

```
importos

defping_host(host):
os.system("ping "+host)
```

---

## Secure Version

```
importsubprocess

defping_host(host):
subprocess.run(["ping",host],check=True)
```

---

# 7.3 Hardcoded Secrets

## Vulnerable Code

```
SECRET_KEY="mysecret123"
```

---

## Secure Version

```
importos

SECRET_KEY=os.getenv("SECRET_KEY")
```

---

# 7.4 Insecure File Handling

## Vulnerable Code

```
file=open(user_input,"r")
data=file.read()
```

---

## Secure Version

```
importos

BASE_DIR="/safe_directory/"

file_path=os.path.join(BASE_DIR,user_input)

ifnotfile_path.startswith(BASE_DIR):
raiseException("Invalid path")

withopen(file_path,"r")asfile:
data=file.read()
```

---

# 7.5 Broken Authentication Logic

## Vulnerable Code

```
ifrequest.user.is_admin:
allow_access()
```

---

## Secure Version

```
ifrequest.user.is_authenticatedandrequest.user.role=="admin":
ifnotrequest.session.is_valid:
deny_access()
else:
allow_access()
```

---

# 8. Advanced Configuration

---

## 8.1 Multi-Job Pipeline Integration

```
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

  security-review:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: anthropics/claude-code-security-review@main
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

---

## 8.2 Combine with SAST

```
- name: Run Snyk Scan
  uses: snyk/actions/python@master

- name: Run Claude Security Review
  uses: anthropics/claude-code-security-review@main
```

---

## 8.3 Custom Wrapper (Optional)

You can wrap Claude with custom prompts:

```
defsecurity_review(code_diff):
prompt=f"""
    Perform a strict application security review.

    Focus on:
    - Injection vulnerabilities
    - Authentication flaws
    - Secrets exposure
    - Business logic issues

    Code:
{code_diff}
    """
```

---

# 9. Developer Workflow (Detailed)

---

## Step 1: Write Code

Developer writes feature or fix

---

## Step 2: Run Local Checks (Optional)

Example script:

```
python local_security_check.py
```

---

## Step 3: Create PR

- Target branch: main
- Add description

---

## Step 4: Automated Security Review

- Claude analyzes diff
- Comments appear in PR

---

## Step 5: Fix Issues

- Address all:
    - High severity → mandatory
    - Medium → required before approval

---

## Step 6: Approval & Merge

- All checks pass
- Reviewers approve
- PR merged

---

# 10. AppSec Operational Procedures

---

## 10.1 Daily Monitoring

- Review flagged vulnerabilities
- Check repeated patterns

---

## 10.2 Weekly Review

Track:

- Top vulnerability types
- Most affected repos
- Developer response time

---

## 10.3 Example Metrics Script

```
importjson

defcount_vulnerabilities(reports):
summary= {"high":0,"medium":0,"low":0}

forrinreports:
summary[r["severity"]]+=1

returnsummary
```

---

# 11. Security Policies

---

## 11.1 Severity Handling

| Severity | Action |
| --- | --- |
| High | Block merge |
| Medium | Must fix |
| Low | Optional |

---

## 11.2 Merge Conditions

PR must NOT be merged if:

- High severity issue exists
- Security review has not completed

---

# 12. Limitations & Controls

---

## 12.1 Known Limitations

- May miss multi-file logic issues
- May not fully understand business context
- Dependent on code diff quality

---

## 12.2 Risk Mitigation

- Combine with:
    - SAST tools
    - Manual review
    - Threat modeling

---

# 13. Best Practices

---

## 13.1 Keep Reviews Focused

- Analyze diffs, not entire repo

---

## 13.2 Do Not Blindly Trust Output

- Validate critical findings manually

---

## 13.3 Train Developers

- Teach:
    - OWASP Top 10
    - Secure coding
    - Reading security feedback

---

## 13.4 Secure Secrets

- Use vaults / secret managers
- Rotate API keys regularly

---

# 14. Example End-to-End Scenario

---

## PR Contains

```
password=input("Enter password: ")
ifpassword=="admin123":
grant_access()
```

---

## Claude Finding

```
Hardcoded Credential Detected

Risk:
Authentication bypass

Fix:
Use secure authentication mechanism and hashed passwords
```

---

## Fixed Code

```
importbcrypt

defverify_password(input_password,stored_hash):
returnbcrypt.checkpw(input_password.encode(),stored_hash)
```

---

# 15. Conclusion

Claude Code Security Review should be used as:

- A **PR-level security gate**
- A **developer assistant for secure coding**
- A **DevSecOps control mechanism**

It is most effective when:

- Integrated into CI/CD
- Enforced via branch protection
- Combined with other security tools
