# Amazon EC2 - Associate Level Summary (SAA-C03)

## 1. IP Addressing (IPv4)
*   **Private IP:**
    *   Internal AWS Network. Cannot be accessed over the Internet.
    *   Remains **static** (unchanged) when the instance is stopped/started.
*   **Public IP:**
    *   Accessible over the Internet. Unique worldwide.
    *   **Dynamic:** Changes automatically when the instance is **Stopped** and **Started**.
*   **Elastic IP (EIP):**
    *   **Static Public IP**. You own it until you delete it.
    *   **Cost:** Free if attached to a running instance. **Cost money** if unattached or attached to a stopped instance.
    *   **Limit:** 5 EIPs per region per account.
    *   **Best Practice:** Avoid using EIPs. Use DNS (Route 53) or Load Balancers instead.

## 2. Placement Groups (To control instance placement strategy)
| Type | Description | Pros/Cons | Use Case |
| :--- | :--- | :--- | :--- |
| **Cluster** | Instances in the same Rack/AZ. | **Pros:** Low latency, 10Gbps bandwidth.<br>**Cons:** High failure risk (Rack failure). | Big Data, HPC, Supercomputing. |
| **Spread** | Instances on distinct hardware (racks). | **Pros:** High availability.<br>**Cons:** Max **7 instances** per AZ per group. | Critical applications. |
| **Partition** | Instances in partitions (set of racks). | **Pros:** Isolate failure domains, scale to 100s of instances.<br>**Cons:** Complex. | Distributed systems (**Hadoop, Cassandra, Kafka**). |

## 3. Elastic Network Interface (ENI)
*   **Definition:** A logical, virtual network card.
*   **Attributes:** Primary Private IP, Secondary Private IPs, Elastic IP, Mac Address, Security Groups.
*   **Scope:** Bound to a specific **Availability Zone (AZ)**.
*   **Failover:** Can be detached from one instance and attached to another (in the same AZ) to move the Private IP (Redirect traffic).

## 4. EC2 Hibernate
*   **Process:** Writes the **RAM** (in-memory state) to the **Root EBS Volume** before stopping.
*   **Benefit:** Faster boot time (apps are pre-loaded).
*   **Requirements:**
    *   Root Volume must be **EBS** and **Encrypted**.
    *   Enabled at instance **Launch**.
    *   Max RAM size < 150 GB.
    *   Instance cannot be hibernated for more than 60 days.
