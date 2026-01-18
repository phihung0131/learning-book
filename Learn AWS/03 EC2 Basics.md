# Amazon EC2 Basics - Summary (SAA-C03)

## 1. EC2 Overview
*   **EC2 (Elastic Compute Cloud):** Infrastructure as a Service (IaaS). Rent virtual machines (instances).
*   **Configuration options:** OS (Linux, Windows, Mac), CPU, RAM, Storage, Network, Firewall (Security Group).
*   **User Data:**
    *   Script launched at the **first boot** of an instance.
    *   Used for bootstrapping (installing updates, software, downloading files).
    *   Runs with **root** privileges.

## 2. EC2 Instance Types (Mnemonic: "Fight McPixie" - *Mẹo nhớ vui*)
*   **General Purpose (m, t):** Balance of Compute, Memory, Networking. (Web servers, code repos).
*   **Compute Optimized (c):** High performance processors. (Batch processing, Media transcoding, Gaming servers, HPC).
*   **Memory Optimized (r, x, z):** Fast performance for workloads that process large data sets in memory. (**Databases**, Cache, Real-time processing).
*   **Storage Optimized (i, d, h):** High, sequential read and write access to large data sets on local storage. (Data Warehousing, OLTP).

## 3. Security Groups (Firewall)
*   **Control traffic:** Inbound and Outbound.
*   **Rules:** Only **Allow** rules (No Deny rules).
*   **Stateful:** If you allow inbound request, the outbound response is automatically allowed.
*   **Troubleshooting:**
    *   **Time out:** Security Group issue (Blocking traffic).
    *   **Connection refused:** Application error or instance not launched.
*   **Default:** Block all inbound, Allow all outbound.
*   **Important Ports:**
    *   **22:** SSH (Linux) / SFTP.
    *   **21:** FTP.
    *   **80:** HTTP.
    *   **443:** HTTPS.
    *   **3389:** RDP (Windows).

## 4. SSH & Connection
| OS | Method | Key Format | Note |
| :--- | :--- | :--- | :--- |
| **Mac / Linux** | Terminal (`ssh` command) | `.pem` | Run `chmod 400 key.pem` permissions. |
| **Windows (<10)** | PuTTY | `.ppk` | Convert `.pem` to `.ppk` using **PuTTYgen**. |
| **Windows (10+)** | PowerShell / CMD | `.pem` | Same as Mac/Linux. |
| **Any OS** | **EC2 Instance Connect** | N/A | Browser-based. Requires Amazon Linux 2 & Port 22 open. |

## 5. EC2 Instance Roles
*   **Best Practice:** Never store AWS credentials (Access Keys) on an EC2 instance.
*   **Solution:** Create an **IAM Role** with necessary permissions and assign it to the EC2 Instance.

## 6. Purchasing Options (Cost Optimization)
| Option | Description | Use Case |
| :--- | :--- | :--- |
| **On-Demand** | Pay by second/hour. Highest cost. No commitment. | Short-term, unpredictable workloads. |
| **Reserved (RI)** | 1 or 3 years commitment. Up to 72% discount. | Steady-state usage (e.g., **Databases**). |
| **Savings Plans** | Commit to usage ($/hour) for 1 or 3 years. Flexible. | Long workloads with flexibility (e.g., changing instance family). |
| **Spot Instances** | Up to 90% discount. Can be lost at any time. | Batch jobs, data analysis, **interruption-tolerant** workloads. |
| **Dedicated Host** | Physical server dedicated to you. | Compliance, **BYOL** (Bring Your Own License). |
| **Capacity Reservation** | Reserve capacity in a specific AZ. | Events requiring guaranteed capacity. No billing discount. |

### Spot Fleets
*   Set of Spot Instances + (optional) On-Demand Instances.
*   **Strategies:**
    *   `lowestPrice`: Most cost-efficient (short workloads).
    *   `diversified`: Great for availability (long workloads).
    *   `capacityOptimized`: Pools with optimal capacity for the number of instances (Recommended).