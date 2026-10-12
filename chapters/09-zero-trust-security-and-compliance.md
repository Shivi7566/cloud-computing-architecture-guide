# Module 9: Cloud Security, Zero Trust & Regulatory Compliance

> **Part of the [Ultimate Cloud Computing Handbook](../README.md)** by [CloudKnowledge.in](https://cloudknowledge.in)
> Author: **Shivam Tiwari** (Senior IAM & Cloud Architect)

---

## Module 9: Cloud Security, Zero Trust & Regulatory Compliance

        

          Perimeter-based security ("castle-and-moat") is obsolete in cloud computing. Modern cloud defense requires an uncompromising Zero-Trust Architecture predicated on the core maxim: "Never trust, always verify, assume breach."
        

        
          
            🛡️
            
#### Identity is the Perimeter

            
Every single service-to-service call must authenticate and authorize dynamically using short-lived cryptographically signed tokens (OAuth2 / OIDC / mTLS).

          
          
            🔑
            
#### Envelope Encryption

            
Data is encrypted using a unique Data Encryption Key (DEK). The DEK is encrypted under a Key Encryption Key (KEK) managed in an HSM-backed KMS.

          
          
            🔍
            
#### Continuous CSPM

            
Cloud Security Posture Management tools continuously audit API configurations to detect open S3 buckets, excessive IAM permissions, and unencrypted databases.

          
        

        
### 9.1 Identity and Access Management (IAM) Best Practices

        

          IAM is the most critical attack surface in any cloud environment. Compromised credentials bypass all firewalls and network segmentation rules.
        

        
          - Banish Long-Lived API Keys: Eliminate static `AWS_ACCESS_KEY_ID` and secret keys from developer machines and CI/CD pipelines. Instead, leverage OIDC (OpenID Connect) Federation allowing GitHub Actions or GitLab CI to assume temporary IAM roles via AWS STS.

          - Enforce Least Privilege with Permission Boundaries: Grant only the exact API actions required. Avoid wildcards (`Action: "*"`, `Resource: "*"`) under all circumstances.

          - Attribute-Based Access Control (ABAC): Scale permission models by evaluating metadata tags (e.g., allow developer Alice to start EC2 instances only if `instance.tag.Project == alice.tag.Project`).

        

        
### 9.2 Envelope Encryption & Cryptographic Key Management (KMS)

        

          Directly encrypting large files with a central master key creates massive performance bottlenecks and exposes the master key over the network. Cloud security solves this via Envelope Encryption:
        

        
          - Your application calls the cloud Key Management Service (AWS KMS, Azure Key Vault, GCP Cloud KMS) requesting a new cryptographic key.

          - KMS generates a plaintext Data Encryption Key (DEK) and an encrypted copy of that same DEK (wrapped under the Customer Master Key / KEK inside an FIPS 140-2 Level 3 Hardware Security Module).

          - Your application encrypts the local data using the plaintext DEK via AES-GCM-256.

          - The plaintext DEK is permanently purged from memory; the encrypted DEK is stored directly alongside the encrypted ciphertext as metadata.

          - To decrypt later, the application sends the encrypted DEK back to KMS, which decrypts it inside the HSM and returns the plaintext key.

        

        
### 9.3 Compliance Frameworks: SOC 2, HIPAA, PCI-DSS & ISO 27001

        

          Cloud infrastructure enables streamlined regulatory audits through automated compliance tooling:
        

        
          - SOC 2 Type II: Evaluates internal controls over security, availability, and confidentiality across an extended observation window (usually 6 to 12 months). Cloud audit trails (AWS CloudTrail, Azure Monitor) serve as immutable evidence.

          - PCI-DSS 4.0: Governs payment cardholder data. Requires micro-segmented networks, end-to-end tokenization, and strict encryption preventing card numbers from ever touching unencrypted application logs.

          - HIPAA: Enforces protection of electronic Protected Health Information (ePHI). Requires signing a Business Associate Agreement (BAA) with your cloud provider and ensuring all datastores employ AES-256 encryption.

---

### 🔗 Essential Resources
- 🌐 **Main Platform:** [CloudKnowledge.in](https://cloudknowledge.in)
- 🖨️ **Print & Desk Cheat Sheets:** [PrintKrlo.in](https://printkrlo.in) (Doorstep delivery for diagrams & notes)
- 📺 **Video Labs & Walkthroughs:** [YouTube: @Cloud-Knowledge](https://youtube.com/@Cloud-Knowledge)
- 🔙 **[Return to Table of Contents](../README.md)**
