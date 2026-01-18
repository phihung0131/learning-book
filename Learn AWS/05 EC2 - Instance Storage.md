# Amazon EC2 - Instance Storage Summary (SAA-C03)

## 1. Amazon EBS (Elastic Block Store)
*   **Definition:** A network drive you can attach to your instances while they run.
*   **Key Properties:**
    *   **Persistent:** Data is preserved after instance termination (unless "Delete on Termination" is unchecked).
    *   **Zone Locked:** Bound to a specific **Availability Zone (AZ)**. To move across AZs, you must snapshot and restore.
    *   **Network Drive:** Uses the network to communicate, has slightly higher latency than physical drives.

### EBS Volume Types (Exam Critical)
| Type | Name | Use Case | Performance Characteristics |
| :--- | :--- | :--- | :--- |
| **General Purpose SSD** | **gp2 / gp3** | Boot volumes, Dev/Test, Virtual Desktops. | **gp3:** Baseline 3,000 IOPS. IOPS & Throughput can be scaled independently. <br>**gp2:** IOPS linked to size (3 IOPS/GB). |
| **Provisioned IOPS SSD** | **io1 / io2 Block Express** | **Critical Databases** (Oracle, MySQL, Cassandra), Apps needing > 16,000 IOPS. | Highest performance. **io1/io2** support **Multi-Attach** (attach to multiple EC2 instances in the same AZ). |
| **Throughput Optimized HDD** | **st1** | Big Data, Data Warehousing, Log processing. | HDD. Max throughput 500 MiB/s. Cannot be a boot volume. |
| **Cold HDD** | **sc1** | File Servers, Infrequently accessed data. | HDD. Lowest cost. Max throughput 250 MiB/s. Cannot be a boot volume. |

## 2. EBS Operations
*   **Snapshots:**
    *   Point-in-time backup of an EBS volume.
    *   Can be copied across AZs or Regions.
    *   **EBS Recycle Bin:** Protects snapshots from accidental deletion (Retention rule: 1 day to 1 year).
    *   **FSR (Fast Snapshot Restore):** Immediate full performance (expensive).
*   **Encryption:**
    *   Data at rest and in flight is encrypted.
    *   **To encrypt an unencrypted volume:** Create Snapshot -> Copy Snapshot (enable encryption) -> Create Volume from encrypted snapshot.

## 3. EC2 Instance Store
*   **Definition:** Physical hardware disk attached to the host server.
*   **Pros:** **Highest I/O performance**, lowest latency (better than EBS io2).
*   **Cons:** **Ephemeral (Temporary)**. Data is **LOST** if the instance is **Stopped** or Terminated.
*   **Use Case:** Cache, Buffer, Scratch data, Temporary content.

## 4. Amazon EFS (Elastic File System)
*   **Definition:** Managed **NFS** (Network File System).
*   **Compatibility:** **Linux** Only (POSIX compliant).
*   **Scope:** **Multi-AZ**. Can be mounted by hundreds of EC2 instances across different AZs simultaneously.
*   **Cost:** 3x more expensive than EBS gp2.
*   **Storage Classes:**
    *   **Standard:** Frequent access.
    *   **EFS-IA (Infrequent Access):** Cost-effective for old files. Use Lifecycle Policy to move files automatically (e.g., after 30 days).

## 5. Storage Comparison Cheat Sheet
| Feature | **EBS** | **Instance Store** | **EFS** |
| :--- | :--- | :--- | :--- |
| **Type** | Network Block Storage | Physical Block Storage | Network File System |
| **Durability** | Persistent | **Ephemeral** (Lost on Stop) | Persistent |
| **Access** | 1 EC2 (mostly) | 1 EC2 | **Multi-EC2** (Hundreds) |
| **Zone** | **1 AZ** | **1 AZ** | **Multi-AZ** |
| **OS** | Linux & Windows | Linux & Windows | **Linux Only** |
| **Best For** | Databases, Boot drives | Cache, Temp files | Shared Web Servers, CMS |