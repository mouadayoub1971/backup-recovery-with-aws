# backup-recovery-with-aws-backup


## 🎯 **Goal of the Solution**

> **To build a centralized, automated, secure, and scalable backup & recovery system across multiple AWS accounts and regions using AWS Backup.**

This solution is designed for **enterprises or large organizations** that:
- Operate across **multiple AWS accounts** (segregation by environment, team, or business unit).
- Operate in **multiple AWS regions** (for availability, latency, and compliance).
- Need to **standardize and automate** backup operations.
- Need **central visibility and control** over backups.
- Want to implement **testing and compliance checks** for backup & restore processes.

---

## 🔧 **What the Solution Does**

Here’s what this solution implements:

| Feature | Purpose |
|--------|---------|
| ✅ **CloudFormation Templates** | Automate infrastructure setup for AWS Backup in each account and region |
| ✅ **CodePipeline & CodeBuild** | CI/CD to deploy and validate backup infrastructure as code |
| ✅ **AWS Backup Vaults per Account/Region** | Local backup storage (primary backups) |
| ✅ **Centralized Vault (Secondary Backup)** | Copies all backups to a central account/region for disaster recovery |
| ✅ **KMS Keys** | Encrypt backups at rest in each account/region |
| ✅ **Service Roles** | Grant AWS Backup and Lambda the right permissions to operate |
| ✅ **SCP (Service Control Policies)** | Restrict what accounts/users can do (e.g., prevent backup copies outside org) |
| ✅ **Restore Testing Plan + Lambda Function** | Automatically test if backups can be restored correctly |
| ✅ **Multi-region EC2 resources** | Demonstrate and test backup/restore scenarios in a distributed setup |


---

## 🧠 **High-Level Architecture**

```
               +-------------------------------+
               |  AWS Organization (Root)      |
               |-------------------------------|
               |   - Backup OU                 |
               |   - Dev/Prod/Test Accounts    |
               +-------------------------------+

 Each Account + Region:
 ┌──────────────────────────┐
 │ AWS Backup Configuration │
 │ - Backup Vault           │
 │ - Backup Plan + Rules    │
 │ - IAM Role for Backup    │
 │ - KMS Key for Encryption │
 └──────────────────────────┘
        ↓ Copy Backups To
 ┌─────────────────────────────┐
 │ Central Backup Account      │
 │ - Secondary Backup Vault    │
 │ - KMS Key                   │
 │ - Lambda for restore test   │
 │ - Reports                   │
 └─────────────────────────────┘
```

---

## 📦 **Primary vs Secondary Backup**

| Concept | Description |
|--------|-------------|
| 🟦 **Primary Backup** | Stored in each account/region's own vault. Useful for fast restore. |
| 🟩 **Secondary Backup** | Copy of backup data stored in a **centralized account/region** for **disaster recovery**, **compliance**, or **cross-region failover**. |

This is a **best practice** in backup systems: always maintain **off-site** or **off-region** backups.

---

## 🌍 **Multi-account / Multi-region Explained**

| Concept | Description |
|--------|-------------|
| 🔹 **Multi-account** | Different AWS accounts for prod/dev/test teams or business units. Improves **security isolation**. |
| 🔸 **Multi-region** | Resources (like EC2, RDS, etc.) are deployed in different AWS regions. Helps with **disaster recovery** and **compliance**. |
| 🛡️ **AWS Organizations** | Master account + all member accounts. Allows **centralized billing**, **SCPs**, and **cross-account permissions**. |

The solution uses **AWS Organizations** to:
- Ensure only trusted accounts can access/copy backups
- Simplify deployment and IAM trust setup between accounts

---

## 🔐 **IAM Roles vs SCPs**

| IAM Role | SCP |
|----------|-----|
| Allow **services** (e.g., AWS Backup, Lambda) to act on resources | Prevent users/accounts from doing unauthorized actions |
| Scoped to a single account (can include trust to others) | Applied **at the organization or OU level** |
| Example: “Allow Backup to use KMS Key” | Example: “Deny copying backup vault to accounts outside the org” |

So:
- IAM = **who can do what** inside an account
- SCP = **what’s allowed in principle** across the org

---

## ✅ **What This Helps You Achieve**

1. **Disaster Recovery Readiness**
   - Backup copies in another account & region.
   - Automated restore validation (with Lambda).
2. **Compliance & Governance**
   - SCPs enforce org-wide rules.
   - KMS for encryption.
   - Centralized logging/reporting.
3. **Infrastructure as Code & CI/CD**
   - CloudFormation templates to replicate this setup easily.
   - CodePipeline & CodeBuild for testing before deployment.
4. **Scalability**
   - Easily extend to more accounts or regions.
   - Framework supports adding new workloads/resources.
