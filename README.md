# 🔒 Project 2: Secure Enterprise Storage with Network Isolation

## 📋 Table of Contents
- [Project Overview](#project-overview)
- [Business Scenario](#business-scenario)
- [Architecture](#architecture)
- [Implementation Steps](#implementation-steps)
- [Security Validation](#security-validation)
- [Lessons Learned](#lessons-learned)
- [Next Steps](#next-steps)

## 🎯 Project Overview

This project demonstrates how to deploy an enterprise-grade storage solution with comprehensive network isolation and security controls. By implementing private endpoints, network security groups, and storage firewalls, we ensure sensitive data is protected from unauthorized access while maintaining accessibility for authorized services.

**Technologies Covered**: Virtual Networks, NSGs, Private Endpoints, Storage Firewalls, Encryption, ARM Templates, Security Monitoring

## 🏢 Business Scenario

A financial company needs to deploy a storage account for sensitive data with the following security requirements:
- ✔️ Isolated from public internet
- ✔️ Accessible only from specific subnets  
- ✔️ Encrypted with customer-managed keys
- ✔️ Monitored for suspicious access
- ✔️ Deployed via automated, repeatable templates
  
## 🏗️ Architecture

The solution implements a three-tier network architecture with dedicated subnets and private connectivity:

VNet (10.1.0.0/16)
├── snet-web (10.1.1.0/24) - Web tier with limited internet access
├── snet-db (10.1.2.0/24) - Database tier with internal-only access
└── snet-privateendpoint (10.1.100.0/24) - Dedicated private endpoint subnet

## 🛠️ Implementation Steps

### 1. Virtual Network Setup

Created a secure network foundation with dedicated subnets for different tiers.

![VNet Subnets](https://github.com/user-attachments/assets/20972523-3664-4c70-8093-540efe6c2586)

![VNet Configuration](https://github.com/user-attachments/assets/c1179315-eed3-4a1d-99f6-e7509e971e44)

**Key Configuration:**
- **VNet**: `vnet-prod-uks` with address space `10.1.0.0/16`
- **Subnets**: 
  - `snet-web` (10.1.1.0/24) - Web applications
  - `snet-db` (10.1.2.0/24) - Database tier  
  - `snet-privateendpoint` (10.1.100.0/24) - Dedicated private endpoints

![Subnet Configuration](https://github.com/user-attachments/assets/fedbb875-4784-45a7-a892-b090b1750894)
*Figure 1: Successfully deployed VNet with three subnets*

### 2. Network Security Group Configuration

Implemented zero-trust networking with explicit allow/deny rules.

![NSG Overview](https://github.com/user-attachments/assets/52b87c14-2aaa-4f2f-b78a-701d8e122645)

**Web Subnet NSG Rules:**
![Web NSG Rules](https://github.com/user-attachments/assets/7faaa02f-609a-4c5e-ae47-aac3c2844a55)

**Rules Configuration:**
- `allowHTTPS` (Priority 100) - Allow HTTPS from internet
- `allowStorageOutbound` (Priority 110) - Allow outbound to Storage service tag
- Default Azure rules maintained for intra-VNet communication

**Database Subnet NSG Rules:**
![Database NSG Rules](https://github.com/user-attachments/assets/ed276ee6-71af-47f2-8f10-e1e9ec95f9ce)

*Figure 2: Database NSG allowing specific web subnet communication*

### 3. Secure Storage Account Deployment

Configured storage with security-first settings and disabled public access.

![Storage Account Overview](https://github.com/user-attachments/assets/eb1b5f5d-12ba-45c2-9397-dde12d0ed48f)
*Figure 3: Storage account deployment configuration*

**Critical Security Configurations:**

**Networking Settings:**
![Networking Configuration](https://github.com/user-attachments/assets/630ae248-10f2-4807-80f2-0113c9bef0be)
- Public access disabled to enforce private-only connectivity
- Network routing: Premium (Microsoft network routing)

**Advanced Security:**
![Advanced Security](https://github.com/user-attachments/assets/786e30fb-aa33-4f09-8752-4ca5506fe79d)
- TLS 1.2 required for all connections

**Data Protection:**
![Data Protection](https://github.com/user-attachments/assets/14915cf0-8056-41d0-8d03-37d52516c231)
- Soft delete enabled for blobs, containers, and file shares (7 days)

**Encryption Configuration:**
![Encryption Settings](https://github.com/user-attachments/assets/952acf75-8000-4ea1-9a7d-22ca0bc3f6d5)
- Microsoft-managed keys with infrastructure encryption enabled
- Support for customer-managed keys configured

### 4. Private Endpoint with DNS Integration

Established secure private connectivity to eliminate public exposure.

![Private Endpoint Configuration](https://github.com/user-attachments/assets/c4cd2f56-45c5-41f4-841b-1df5fcf44439)

**Configuration Details:**
- Resource type: `Microsoft.Storage/storageAccounts`
- Target subresource: `blob`
- DNS integration enabled for automatic private name resolution

![Private Endpoint Success](https://github.com/user-attachments/assets/04dda7d0-eaeb-485d-afc7-d0b02cae3716)
*Figure 4: Successfully deployed private endpoint*

### 5. Advanced Encryption & Security

Enhanced data protection with customer-managed keys and comprehensive monitoring.

![CMK Configuration](https://github.com/user-attachments/assets/09449dc1-0e0a-403a-9e28-80a3e1c4811a)

**Security Enhancements:**
- Azure Key Vault integration for customer-managed keys
- Managed identity for secure storage account access
- Advanced threat protection enabled

## 🔍 Security Validation

### 6.1 Negative Testing: erify Public Access is Blocked

Attempted to access the storage account via public URL in a browswer. Access was denied.

![Storage Account URL](https://github.com/user-attachments/assets/2d05db87-bfc6-490a-b0f2-99adadaf9156)

**Test Result:** ✔️ SUCCESS
![Access Blocked](https://github.com/user-attachments/assets/8b18f12c-9dad-466b-b065-5fd8e5a510ef)
*Public access successfully blocked*

### 6.2 Positive Testing: Private Endpoint Access

*Planned to create a VM in the web subnet to test internal connectivity. However, VM deployment was blocked due to region restrictions (UK South not supported in free tier).*

*Note: Testing methodology prepared for:*
- Deploy test VM in `snet-web` subnet
- Validate private endpoint connectivity
- Verify cross-subnet communication controls

## 💡 Lessons Learned

### 🔒 Security Insights
- **Private endpoints provide more secure connectivity** than service endpoints alone by eliminating public exposure entirely
- **Explicit "Deny" rules in NSGs are safer** than relying on implicit defaults but one must always define explicit security boundaries
- **Dedicated subnets for private endpoints** significantly simplify network security group management and follow Azure best practices
- **DNS configuration is critical** for private endpoint functionality. Without proper DNS, private endpoints fail

### ⚙️ Technical Implementation
- **Service tags (like 'Storage')** enable dynamic IP management and reduce maintenance overhead compared to static IP rules
- **Priority values in NSG rules** determine evaluation order (lower numbers processed first, making custom rules override defaults)
- **Regional service availability** can impact project planning ( always verify resource availability before architecture finalization)
- **Infrastructure encryption** adds an additional layer of security but requires understanding of key management implications

### 🎯 Architectural Decisions
- **Three-tier subnet design** (web, database, private endpoints) provides clear separation of concerns and security boundaries
- **Disabling public access at creation** prevents accidental exposure versus trying to secure it later
- **Premium network routing** offers better security but requires cost-benefit analysis for production workloads
- **Soft delete configurations** provide data protection without significant performance impact

### 📝 Operational Considerations
- **Documenting security decisions** during implementation helps with future audits and troubleshooting
- **Testing public access blocking** is crucial as configuration doesn't always equal actual protection
- **Clean resource naming conventions** improve manageability and follow cloud adoption framework principles
- **Regional constraints** should be identified during the planning phase to avoid project delays

## End of Project 
This project has been paused due to a region limitation in the free tier. I create the VNet  in UK South, which does not support VM deployment under my current plan. I will recreate the environment in a supported region to complete the connectivity test.
---

*Last Updated: November 2024*
