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

## Author

**Koffi Jean-Marie Amedjonekou**
Security Researcher

## License

This project is shared for educational and portfolio purposes.
