# Module 11: Enterprise Cloud Migration Blueprints (The 7 Rs)

> **Part of the [Ultimate Cloud Computing Handbook](../README.md)** by [CloudKnowledge.in](https://cloudknowledge.in)
> Author: **Shivam Tiwari** (Senior IAM & Cloud Architect)

---

## Module 11: Enterprise Cloud Migration Blueprints (The 7 Rs)

        

          Migrating enterprise workloads from on-premises datacenters to the cloud is a complex socio-technical undertaking. Gartner's renowned 7 Rs Framework provides the standard classification system for evaluating legacy application portfolios:
        

        
          
            
#### 1. Rehost (Lift & Shift)

            
Moving virtual machine disk images directly to cloud IaaS (EC2/Azure VMs) with zero code modifications. Fast and low-risk, but fails to realize cloud-native elasticity.

          
          
            
#### 2. Replatform (Lift, Tinker & Shift)

            
Replacing legacy self-managed infrastructure components with managed services (e.g., migrating self-hosted MySQL on Linux to Amazon Aurora) without altering core business code.

          
          
            
#### 3. Refactor (Re-architect)

            
Decomposing monolithic applications into microservices, containerizing workloads, and implementing event-driven serverless architectures to maximize agility.

          
          
            
#### 4. Repurchase (Drop & Shop)

            
Retiring legacy custom-built tools in favor of commercial Software as a Service (SaaS) platforms (e.g., replacing on-premises Microsoft Exchange with Google Workspace or Microsoft 365).

          
          
            
#### 5. Retain (Revisit)

            
Keeping mission-critical legacy applications on-premises because migration is currently cost-prohibitive, technically blocked, or constrained by strict data sovereignty laws.

          
          
            
#### 6. Retire

            
Discovering through portfolio analysis that 10%–20% of enterprise applications are obsolete and no longer used, allowing immediate decommission and immediate cost savings.

          
          
            
#### 7. Relocate (Hypervisor Migration)

            
Transporting VMware or Nutanix virtualization clusters directly to dedicated bare-metal cloud nodes (e.g., VMware Cloud on AWS) without converting disk formats.

          
        

        
### 11.1 The Live Database Migration Runbook

        

          The most critical phase of any migration is moving multi-terabyte production databases with zero transaction loss:
        

        
          - Schema Conversion: Use the AWS Schema Conversion Tool (SCT) to translate proprietary schemas (Oracle PL/SQL or SQL Server T-SQL) into open PostgreSQL or MySQL syntax.

          - Initial Bulk Load: Export a full snapshot of the source database and load it onto the target cloud database instance.

          - Change Data Capture (CDC): Establish a replication stream using AWS Database Migration Service (DMS) or Debezium. CDC continuously reads the transaction redo/WAL logs on-premises and applies ongoing changes to the cloud replica in near-real-time.

          - Cutover Execution: Validate data parity, point the application to the new cloud endpoint, and terminate the replication stream during a 5-minute scheduled read-only maintenance window.

---

### 🔗 Essential Resources
- 🌐 **Main Platform:** [CloudKnowledge.in](https://cloudknowledge.in)
- 🖨️ **Print & Desk Cheat Sheets:** [PrintKrlo.in](https://printkrlo.in) (Doorstep delivery for diagrams & notes)
- 📺 **Video Labs & Walkthroughs:** [YouTube: @Cloud-Knowledge](https://youtube.com/@Cloud-Knowledge)
- 🔙 **[Return to Table of Contents](../README.md)**
