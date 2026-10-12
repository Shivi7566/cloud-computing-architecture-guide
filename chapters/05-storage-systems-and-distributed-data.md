# Module 5: Storage Architecture & Distributed Data Systems

> **Part of the [Ultimate Cloud Computing Handbook](../README.md)** by [CloudKnowledge.in](https://cloudknowledge.in)
> Author: **Shivam Tiwari** (Senior IAM & Cloud Architect)

---

## Module 5: Storage Architecture & Distributed Data Systems

        

          Storage in the cloud is decoupled from compute. Understanding the performance profiles, consistency guarantees, and cost structures of Block, Object, and File storage determines application performance and scalability.
        

        
### 5.1 Storage Types: Block vs. Object vs. File

        
          
| Dimension | Block Storage (EBS / Azure Disk / PD) | Object Storage (S3 / Azure Blob / GCS) | File Storage (EFS / Azure Files / Filestore) |
| --- | --- | --- | --- |
| Protocol / Interface | NVMe / iSCSI block device | REST API (HTTP GET, PUT, DELETE) | NFSv4 / SMB network filesystem |
| Latency | Sub-millisecond to single-digit ms | 10ms - 100ms first-byte latency | Low single-digit milliseconds |
| Concurrent Attach | Single instance (Multi-Attach limited) | Virtually infinite concurrent clients | Thousands of concurrent instances |
| Max Capacity | Up to 64 TiB per volume | Virtually limitless exabyte scale | Elastic; grows automatically |
| Cost per GB/Month | $0.08 - $0.125 / GB (Provisioned) | $0.021 - $0.023 / GB (Standard) | $0.30 / GB (Provisioned or bursting) |
| Primary Use Cases | OS boot disks, transactional databases | Data lakes, backups, media assets, logs | Legacy apps, shared CMS media, ML datasets |

        

        
### 5.2 Deep Dive: Amazon S3 Strong Consistency & Lifecycle Management

        

          In late 2020, Amazon S3 achieved a monumental engineering breakthrough: Strong Read-After-Write Consistency for all PUT and DELETE requests of objects across all regions, without changes to performance or availability. Previously, S3 operated on eventual consistency, requiring complex workarounds for Big Data analytics engines (like Hadoop or Apache Spark) reading newly committed files.
        

        

          To maintain financial efficiency, enterprise storage architectures configure automated S3 Lifecycle Rules:
        

        
          - Day 0: Files ingested into S3 Standard (frequently accessed data).

          - Day 30: Transitioned to S3 Standard-Infrequent Access (Standard-IA) (reduces storage costs by ~50%; incurs retrieval fees).

          - Day 90: Transitioned to S3 Glacier Flexible Deep Archive (costs ~$0.00099/GB/mo, an 80% reduction; retrieval takes 3-5 hours).

          - Day 365+: Automated permanent deletion or cryptographic compliance retention (S3 Object Lock).

        

        
### 5.3 The CAP Theorem in Modern Distributed Databases

        

          Eric Brewer's CAP Theorem dictates that in any asynchronous distributed network subject to partition failure (P), a system can guarantee either Consistency (C) or Availability (A), but never both simultaneously:
        

        
          - CP Systems (Consistency + Partition Tolerance): In the event of a network split between datacenters, the system rejects writes to maintain atomic truth and prevent split-brain syndrome. Examples: MongoDB replica sets, etcd, Apache HBase.

          - AP Systems (Availability + Partition Tolerance): In the event of a network split, all nodes remain available to accept writes, sacrificing immediate consistency in favor of eventual consistency. Examples: Amazon DynamoDB (default eventual read mode), Apache Cassandra, Couchbase.

        

        
### 5.4 The Modern Data Architecture: Lakehouses & Apache Iceberg

        

          The enterprise data landscape has converged from separate Data Warehouses (structured SQL data) and Data Lakes (unstructured raw files) into the Data Lakehouse. Open table formats like Apache Iceberg, Delta Lake, and Apache Hudi bring full ACID transactional guarantees, time-travel queries, and schema evolution directly to inexpensive cloud object storage (S3/GCS/Blob).
        

        

          Query engines like Amazon Athena (Presto/Trino), Google BigQuery, and Snowflake can query these open Iceberg tables directly without requiring costly data ingestion or vendor lock-in.

---

### 🔗 Essential Resources
- 🌐 **Main Platform:** [CloudKnowledge.in](https://cloudknowledge.in)
- 🖨️ **Print & Desk Cheat Sheets:** [PrintKrlo.in](https://printkrlo.in) (Doorstep delivery for diagrams & notes)
- 📺 **Video Labs & Walkthroughs:** [YouTube: @Cloud-Knowledge](https://youtube.com/@Cloud-Knowledge)
- 🔙 **[Return to Table of Contents](../README.md)**
