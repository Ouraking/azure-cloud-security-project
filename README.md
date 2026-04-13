# Cloud Security Implementation

A comprehensive Azure IaaS cloud migration and security hardening project for a national shipping company transitioning from on-premises to cloud infrastructure.

## Overview

This project addresses the secure migration of a 2,000-employee shipping company (established 1977) from on-premises infrastructure to Microsoft Azure IaaS. The company, a US government contractor processing card transactions, faced critical security gaps following a disgruntled employee's departure, including violated least-privilege principles and unverified backup systems.

## Key Deliverables

- **Cloud Service Model Selection:** IaaS evaluation with cost-benefit analysis
- **Identity & Access Management:** Department-specific RBAC with least-privilege enforcement
- **Key Vault Security:** Hardened encryption key management with soft delete and purge protection
- **Encryption Strategy:** Data-at-rest and data-in-transit protection recommendations
- **Backup Configuration:** Automated daily backups with geo-redundant disaster recovery
- **Risk Analysis & Compliance:** Threat assessment with mitigation countermeasures

## Technical Implementation

### Identity & Access Management (IAM)

- **Department-Specific Resource Groups:** Isolated access for Accounting, Marketing, and IT
- **Contributor Role Assignment:** Users can only create, modify, or delete resources within their own department
- **IT Backup Contributor:** IT department granted cross-department backup permissions without data access
- **Just-in-Time Access:** Elevated permissions for IT Key Vault administration

### Key Vault Security

- **Department-Isolated Key Vaults:** Separate vaults for Marketing, Accounting, and IT
- **Soft Delete:** 90-day recovery window for accidentally or intentionally deleted keys
- **Purge Protection:** Prevents permanent deletion during retention period, even with superuser access
- **Granular Access Policies:**
  - Marketing/Accounting: Get, List, Decrypt, Encrypt (read-only key usage)
  - IT: Additional Backup and Restore permissions for disaster recovery

### Encryption

| Protection Layer | Technology | Details |
|-----------------|------------|---------|
| **Data at Rest** | Azure SSE + ADE | Customer-managed keys (CMK) in department Key Vaults, BitLocker/dm-crypt |
| **Data in Transit** | TLS 1.2 | Encryption between clients and Azure services |
| **VPN Tunnels** | IPsec/IKE | Encrypted tunnels via Azure VPN Gateway |

### Backup Configuration

- **Schedule:** Daily at 7:00 PM ET
- **Instant Recovery Snapshots:** 3-day retention
- **Recovery Points:** 45-day retention
- **RPO:** 1 day | **RTO:** 36 hours
- **Cross-Region Restore:** Geo-redundant storage for disaster recovery

## Threat Analysis & Mitigation

### 1. Advanced Persistent Threats (APTs)
- Azure Advanced Threat Protection (ATP) for real-time behavioral analysis
- Network segmentation via Azure Virtual Network and NSGs
- Mandatory MFA for all users, especially privileged accounts

### 2. Data Exfiltration
- Data Loss Prevention (DLP) policies to monitor and block unauthorized data movement
- Azure Storage Analytics for detailed audit trails and forensic investigation

### 3. Cryptographic Key Compromise
- Hardware Security Module (HSM) for key protection
- 90-day key rotation policies
- Key usage auditing and alerting
- Split key management with dual control

## Compliance Framework

| Framework | Requirement | Implementation |
|-----------|-------------|----------------|
| **FISMA** | Federal information security for government contractors | Azure Policy, Security Center, NIST 800-53 controls |
| **PCI-DSS** | Payment card data protection | Key Vault encryption, access controls, audit logging |
| **NIST SP 800-53** | Security and privacy controls | RBAC, least privilege, continuous monitoring |

## Shared Responsibility Model (IaaS)

| Responsibility | Owner |
|---------------|-------|
| Physical infrastructure, hypervisor, host OS | Microsoft Azure |
| Guest OS patching, application security, data encryption | Customer (X LLC) |
| Identity infrastructure, threat detection | Shared |

## Compliance Recommendations

1. **Azure Policy & Blueprints:** Enforce encryption, tagging, and resource standards automatically
2. **Azure Security Center & Sentinel:** Continuous security assessment and threat detection with compliance reporting
3. **Cloud Security Governance Framework:** Defined roles, responsibilities, approval processes, and regular security audits

## Technologies & Tools

- **Cloud Platform:** Microsoft Azure (IaaS)
- **IAM:** Azure RBAC, Resource Groups, Role Assignments
- **Encryption:** Azure Key Vault, SSE, ADE, TLS 1.2, IPsec/IKE
- **Security:** Azure ATP, NSGs, Azure Security Center, Azure Sentinel
- **Backup:** Azure Backup, Geo-Redundant Storage (GRS)
- **Compliance:** Azure Policy, Azure Blueprints

## Architecture Decision Records

### Why IaaS over PaaS?
The company operates legacy applications that require OS-level configuration and custom middleware. PaaS would have forced application refactoring — a 6-12 month effort that conflicted with the urgent security remediation timeline. IaaS provides **lift-and-shift capability** while maintaining full OS control for hardening, patching, and custom security agent deployment. The shared responsibility model was a deliberate trade-off: accepting OS patching responsibility in exchange for deployment speed.

### Why department-isolated RBAC?
The insider threat that triggered this project (a disgruntled employee) exploited **flat access permissions** where any user could reach any department's resources. Department-isolated Resource Groups with Contributor roles ensure that a compromised credential in Marketing cannot access Accounting's financial data or IT's administrative tools. This is the practical application of NIST SP 800-53 AC-6 (Least Privilege) — not just restricting permissions, but architecturally preventing lateral movement.

### Why Key Vault with soft delete and purge protection?
The insider threat scenario included the possibility of intentional key destruction. Without soft delete, a malicious admin could permanently destroy encryption keys, rendering all encrypted data irrecoverable. The **90-day soft delete window** provides a recovery path, while **purge protection** prevents even Global Admins from bypassing the retention period. This is a critical control for government contractors where data destruction could trigger FISMA compliance violations.

### Why 7:00 PM ET backup schedule?
Backup windows must avoid peak transaction hours for a shipping company processing government contracts. 7:00 PM ET falls after close of business on the East Coast but before West Coast operations wind down, minimizing performance impact while ensuring the most recent full business day is captured. The 45-day retention and 36-hour RTO were derived from the company's business continuity requirements and FISMA recovery expectations.

## Threat Model Summary

| Threat | Likelihood | Impact | Mitigation | Framework Alignment |
|--------|-----------|--------|------------|-------------------|
| Insider data exfiltration | **High** (prior incident) | Critical | DLP policies, RBAC isolation, Storage Analytics audit trails | NIST AC-6, AU-6 |
| APT targeting government data | Medium | Critical | Azure ATP, network segmentation, mandatory MFA | NIST SI-4, IA-2 |
| Cryptographic key compromise | Low | Critical | HSM protection, 90-day rotation, split key management | NIST SC-12, SC-13 |
| Ransomware on guest OS | Medium | High | Daily backups, geo-redundant storage, 45-day retention | NIST CP-9, CP-10 |
| Privilege escalation | Medium | High | JIT access, Contributor-only roles, no standing admin | NIST AC-2, AC-6 |

## Lessons Learned

- **Insider threats require architectural controls, not just policies:** The original breach occurred despite having an acceptable use policy. Technical enforcement (RBAC isolation, Key Vault purge protection) is the only reliable countermeasure.
- **Shared responsibility must be documented explicitly:** The IaaS model left guest OS patching as a customer responsibility. Without clear ownership assignment, this task was initially overlooked — a common gap in cloud migrations.
- **Backup testing is as important as backup configuration:** Configuring Azure Backup is straightforward; validating that a 36-hour RTO is actually achievable under realistic conditions required dedicated testing cycles.
- **Compliance is not security — but it provides structure:** FISMA and PCI-DSS requirements provided a minimum baseline, but the actual security architecture exceeded compliance requirements based on the specific threat model.

## Skills Demonstrated

`Azure IaaS Migration` `RBAC Design` `Azure Key Vault` `Encryption (SSE, ADE, TLS, IPsec)` `Insider Threat Mitigation` `Backup & Disaster Recovery` `Threat Modeling` `FISMA Compliance` `PCI-DSS Compliance` `NIST SP 800-53` `Azure Sentinel` `Security Architecture`

## Author

**Koffi Jean-Marie Amedjonekou**
Cybersecurity Engineer

## License

This project is shared for educational and portfolio purposes.
